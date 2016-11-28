title: Upgrade from Hexo 2.8.2 to 3.1.1
categories: 
  - Coding
tags:
  - Hexo
  - Notes
date: 2015-10-30 15:18:13
---

Recently I decided to update my Hexo blog to the latest version and start to writing posts again. The process was not that smooth. So I wrote this post in case anyone needs any help. 

## Upgrade Hexo

The breaking changes in 3.1.1, to me, are that many modules within hexo before are divided further into independent ones. It is hard to update because the structure is so different, not as simple as just update the hexo CLI. We need to do way more than that. 

The first thing is to follow the document<sup>[[1]](#References)</sup> provided by the developer. It is said to be of help when migrating from 2.x to 3.0. 

### Modify package.json

Add hexo data to package.json.

```
{
  "hexo": {
    "version": ""
  }
}
```

### Clean cache

```
hexo clean
```

### Install hexo-cli

```
npm install hexo-cli -g
```

### Install Hexo

```
npm install hexo --save
```

### Install generators

```
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
```

If you use rss in your blog, you should also install the feed generator. 

```
npm install hexo-generator-feed --save
```

### Install server

```
npm install hexo-server --save
```

### Install deployers

```
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
```

### Update plugins

```
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```

## Upgrade Theme

The theme I have been using is derived from Jacman<sup>[[2]](#References)</sup>. I did lots of tuning on the details and disconnected it from its original git repo. So it is hard for me to upgrade it just by git pull. 


### Page not rendered

The problem I first had was the page just didn't show. It showed a bunch of unprocessed ejs code instead like below. 

```
<%- partial('_partial/head') %>
<%- partial('_partial/header', null, {cache: !config.relative_link}) %>
<%- body %>
<% if (theme.sidebar && theme.sidebar !== 'bottom'){ %> <%- partial('_partial/sidebar', null,     {cache: !config.relative_link}) %> <% } %>
<%- partial('_partial/footer', null, {cache: !config.relative_link}) %>
<%- partial('_partial/mobile-nav', null, {cache: !config.relative_link}) %> <%- partial('_partial/after-footer') %>
```

I found the solution in the common hexo problems post<sup>[[3]](#References)</sup>. It is very simple. Just run ``npm install`` under your blog post directory again to install and enable the ejs renderer. 

### Author img

Alright, now the page shows and everything looks good. After I scrolled to the bottom, I found that the author img is not showing. The log in browser debugging tool simply said that the img is not found on server. 

But the image is in its directory, not changed at all. That leads me to the answer that the path is wrong. The root path should be "/". But it is "/css" in the log. 

The problem was noticed by the Jacman theme developer. You can find his post on how to support Jacman with hexo 3.0<sup>[[4]](#References)</sup>. The reason is the code in ``source/css/_base/variable.styl``

```
root = hexo-config("root")
```

This line reads root path from config. In hexo 3.0 if root path is set to "/" or not left blank, it would fail to get the correct path. 

The fix is to change ``hexo-config("root")`` to ``../``. 

The result line should be like below. 

```
root = "../"
```

### Categories and Tags

This is another problem introduced by previous version of Jacman theme. It failed to leave out the zero-post categories and tags, which is very annoying if you have lots of new categories or tags in your draft folder. 

To solve this problem, you can update to the latest version or just add a if block to check post length. 
`themes/Jacman/layout/_partial/widget/tag.ejs`

```
<% if (site.tags.length){ %>
<div class="tagslist">
  <p class="asidetitle"><%= __('tags') %></p>
    <ul class="clearfix">
    <% site.tags.sort('posts.length','desc').limit(20).each(function(item){ %>
      <% if (item.posts.length){ %>
        <li><a href="<%- config.root %><%- item.path %>" title="<%= item.name %>"><%= item.name %><sup><%= item.posts.length %></sup></a></li>
      <% } %>
    <% }); %>
    </ul>
</div>
<% } %>
```

The same goes for `themes/Jacman/layout/_partial/widget/category.ejs`.

``` 
<% if (site.categories.length){ %>
<div class="categorieslist">
  <p class="asidetitle"><%= __('categories') %></p>
    <ul>
    <% site.categories.sort('name').each(function(item){ %>
      <% if (item.posts.length > 0) { %>
        <li><a href="<%- config.root %><%- item.path %>" title="<%= item.name %>"><%= item.name %><sup><%= item.posts.length %></sup></a></li>
      <% } %>
    <% }); %>
    </ul>
</div>
<% } %>
```

### Relative links

When I tried to use other themes for a change, I found that the links for posts were wrong. This seems to be a bug for hexo 3.1.1<sup>[[5]](#References)</sup>. 

The temporary fix is to set relative_link to false in _config.yml for now. Hope that it would be fixed soon. 

### Code blocks

Then I opened some posts to double check everything was good. It turned out that all code blocks are not working anymore. The line number is always 1 not matter how many rows in it. And the syntax was not highlighted either. This is also caused by hexo 3.1.1. The reason is that highlight.js interpret the code as plain text. 

We can manually set language for each code block. But that would be too much work. Instead, we can set auto language detection of highlight.js. Add the config below to manually enable detection in _config.yml. 

```
highlight: 
  auto_detect: true
```

## References

1. [Migrating from 2.x to 3.0](https://github.com/hexojs/hexo/wiki/Migrating-from-2.x-to-3.0)
2. [如何使用 Jacman 主题](http://wuchong.me/blog/2014/11/20/how-to-use-jacman/)
3. [Hexo常见问题解决方案](https://xuanwo.org/2014/08/14/hexo-usual-problem/)
4. [Jacman 支持 Hexo 3](http://wuchong.me/blog/2015/03/12/support-jacman-to-Hexo-3/)
5. [Hexo to use relative path instead of URL](https://github.com/hexojs/hexo/issues/592)
