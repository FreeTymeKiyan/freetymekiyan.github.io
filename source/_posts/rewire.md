title: Rewire-Expose Private in Tests
categories: 
- Coding
tags: 
- Node.js 
- Test 
---

Here is the thing. I'm definitely a newbie in code testing. The same goes for Node.js, I bet. Although I have some former experience with development, those were all about actual implementation. Now I am doing intern at Modulus, which requires more than 80% of test coverage of code. After I finished functional part, my testing adventure started.  
##Problem
One problem I had is that, I got some functions in a module that called some other function directly. This makes testing hard if we want to figure out whether that function is actually called, because we are not calling the exposed function.  

One way to solve it is to create an object which will include all the private functions and expose that object when under test environment. This is a very great workaround inspired by my friends and I'm now using it in my code. But that's not what I'm going to introduce.  
##Introduction
Let me share the second way with you. I found this in a slide that talks about Node.js unit testing(1). So how to test private methods? The answer is using rewire module(2).  

Rewire aims at easy dependency injection for Node.js unit testing. Basically, during testing, we can require rewire module and use rewire rather than require to import the file to be tested. It will be exactly the same in our test environment as under regular circumstances.  

Rewire makes it possible for us to do several things:  

* inject mocks for other modules or globals like process
* leak private variables
* override variables within the module  

###Theory
How is it possible for rewire to do that? 
```
(function (exports, require, module, __filename, __dirname) {
  var method = function () {};
  exports.__set__ = function (name, value) {
    eval(name " = " value.toString());
  };
  exports.__get__ = function (name) {
    return eval(name);
  };
});
```  
We can see clearly from the source code of rewire that it injects **\_\_set\_\_** and **\_\_get\_\_** methods while loading module. These two methods enables us to reach private variables.
###Installation
``` bash
npm install --save-dev rewire
```
##Comparison
###Example
Let's say we have a file called index.js. There are two methods in it, named "someFunc" and "anotherFunc". That someFunc is called by anotherFunc. We wanna test whether someFunc is called or not.
``` javascript
/* in index.js */
var someFunc = function() {
  // function body
};

var anotherFunc = function() {
  someFunc();
};
``` 
###Workaround
So, with the workaround I mentioned above, we may create an object called private and assign all private functions as its properties. Then we can export private under test environment. When we need to call function inside module, we should do add "private." before the name of that function.
``` javascript
/* in index.js */
var private = {};

private.someFunc = function() {
  // function body
};

private.anotherFunc = function() {
  // when another function calls someFunc
  private.someFunc();
}

// export under test environment
if('test' === process.env.NODE_ENV) {
  exports.private = private;
}
```
In test file we need to hook a sinon spy(or stub) to the existing method before test. It should be restored after that.
``` javascript
/* in index-spec.js */
var index = require('index.js').private;
var sinon = require('sinon');

describe('index', function() {
  describe('#anotherFunc', function() {
    // set a stub or spy for the function
    var someFuncSpy;
    before(function () {
      someFuncSpy = sinon.spy(index, 'someFunc');
    });

    it('should someFunc be called', function () {
      index.anotherFunc();
      someFuncSpy.called.should.be.true;
    }); 

    after(function() {
      someFuncSpy = sinon.restore();
    });
  });
});
```
###Rewire
Let's see how that goes with rewire.
``` javascript 
/* in index-spec.js */
// Require rewire in your test file.
var rewire = require('rewire');
// Rewire the file to be tested. 
var index = rewire('./lib/index.js');

describe('index', function() {
  describe('#anotherFunc', function() {
    // save original function for restoration
    var someFunc;
    before(function () {
      // get private function with rewired module
      someFunc = index.__get__('someFunc');
      // override someFunc with a spy
      index.__set__('someFunc', sinon.spy());
    });

    it('should someFunc be called', function () {
      index.anotherFunc();
      someFuncSpy.called.should.be.true;
    }); 

    after(function() {
      // restore someFunc manually
      index.__set__('someFunc', someFunc);
    });
  });
});
```
Originally, I wanted to do something like this:
``` javascript
var someFuncSpy;
before(function () {
  // no hook to existing method, just create a new spy
  someFuncSpy = sinon.spy(index.__get__('someFunc'));
});

it('should someFunc be called', function () {
  index.anotherFunc();
  someFuncSpy.called.should.be.true;
}); 

after(function() {
  someFuncSpy.restore();
});
``` 
As I commented above, this won't work due to the rigid way of binding a spy with existing method. We can only get exposed method with **sinon.spy(index, 'someFunc');**, or **sinon.stub(index, 'someFunc', function() {});**. Even though we can get private functions with rewire, we can't hook it up with a spy in the original module. We could have much more proress. What a pity!  

Anyways, with rewire, we don't need to change our original code. Isn't that great?  

There are more examples on [rewire's repository on Github](https://github.com/jhnns/rewire).
##Conclusion
To make the slightest change to our code while testing, I think it would be better if we use **rewire** module. I'm pretty sure to use rewire for future testing, what about you?
##Reference
1. [html5ify.com, *NODE.JS UNIT TESTING* ](http://html5ify.com/unittesting/slides/index.html#/34)
2. [Github, *jhnns/rewire*](https://github.com/jhnns/rewire)