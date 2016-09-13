title: exports and module.exports
categories:
- Coding
tags:
- Node.js
date: 2014-07-03 09:50:07
---
This is also something happened during my script runner project at Modulus. When I was checking why those exported methods were not called, I was suspicious about the actual differences between ``exports.someFunc`` and ``module.exports.someFunc``. I read about it in my supervisor's post[(1)](#Reference) before, which said that these two are the same. In order to be more clear, I did some more research. Here is what I learned.
## Theory
Let me start with the most formal information. The manual of Node.js did some explanation in *Modules/The module Object*[(2)](#Reference).
### module.exports
First of all, what is ``module.exports``?

>The ``module.exports`` object is created by the Module system. In each module, the `` module `` variable is a **reference** to the object representing the current module.

Sometimes we want our module to be an instance of some class. To do this assign the desired export object to ``module.exports``. Note that assignment to ``module.exports`` must be done immediately. It cannot be done in any callbacks.

``module`` isn't actually a global but rather **local** to each module.

For convenience, ``module.exports`` is also accessible via the ``exports`` **module-global**.

### exports
``exports`` is an alias.

>The ``exports`` variable that is available within a module starts as a reference to ``module.exports``. As with any variable, if you assign a new value to it, it is no longer bound to the previous value.

Imagine that you have created a module. It means that there is a hidden line like ``var exports = module.exports`` in your code even if you didn't writing anything.

>Note that assigning the desired object to ``exports`` will simply rebind the local ``exports`` variable.

This means when we require that module after ``exports = something``, we won't get that something we want.

### require()
Here is a hypothetical implementation of ``require()``:
``` javascript
function require() {
  //...
  function (module, exports) {
    // Your module code goes here
    exports = some_func;

    module.exports = some_func;
  } (module, module.exports); // exports is not passed!
  return module;
}
```

This piece of code is also from the manual. We can find out clearly that some_func is assigned to exports, but exports is never passed on.
## Test
An easy test[(3)](#Reference) of the above theory goes like this:
``` javascript
// module, me.js
module.exports = 'This is me';
exports.name = function() {
  console.log('My name is Yang Liu');
};
```
``` javascript
// another file
var me = require('./me');
me.name();
```
The output will be :
>TypeError: Object This is me has no method 'name'


## More
In another post[(4)](#Reference) about this problem, I found one which said that we should use the following pattern to export something in Node.js by assignment:
``` javascript
exports = module.exports = {
  ...
};
```
As far as I'm concerned, it is totally unnecessary because Node already did that for us, just like what I showed above in the hypothetical code. Just use exports should be fine unless you override it with something else.
## Conclusion
* ``module.exports`` is the real boss.
* ``exports`` is just an alias.
* Override ``module.exports`` in callbacks won't work.
* Just use ``module.exports`` if you don't understand this magic.

## Reference
1. [fiveisprime, *exports or module.exports?*](http://fiveisprime.com/2013/04/18/module-exports/)
2. [Node.js Manual  & Documentation, *Modules*](http://nodejs.org/docs/latest/api/modules.html#modules_the_module_object)
3. [Hack Sparrow, *Node.js Module – exports vs module.exports*](http://www.hacksparrow.com/node-js-exports-vs-module-exports.html)
4. [TimNew's Lab, *Exports vs module.exports in node.js*](http://timnew.github.io/blog/2012/04/20/exports_vs_module_exports_in_node_js/)
