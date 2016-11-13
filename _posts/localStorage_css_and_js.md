---

layout: post  
title: 将静态文件缓存到html5的localStorage，给网站加速  
date: 2014-07-30 11:06:00
tags: [javascript,html5]  
category: 前端开发  
comments: true

---

html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁

本文要说的是html5的localStorage本地储存方式，localStorag可以说是cookie的一个加强版，相比localStorag来说，cookie的限制太多，限制大小4K，并在某些浏览器下还有域名限制；好的是现在html5提供的localStorag比较牛*，有5M的大小，因此爱折腾的小伙伴可以用它来做一些有意思东西


使用localStorage（本地缓存）的优缺点：
优点：

 - 用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的
 - 可以通过chrome浏览器Resources/Local Storage来查看
 - 容量大，易用，原生支持

缺点：

 - 兼容性问题（IE8以下的版本不支持） 
 - 安全性问题，不适用于保存敏感的数据


对于本文要做的事情就是把js和css静态文件保存到本地储存，所以在我们眼里，缺点基本上可以忽略不计
<!-- more -->

实现代码：
``` javascript
var whir = window.whir || {};
whir.res = {
  //页面版本，也由页面输出，用于刷新localStorage缓存
  pageVersion: "0.0.1", 
  loadJs: function (name, url, callback) {//动态加载js文件并缓存
      //判断浏览器是否支持HTML5本地储存
      if (window.localStorage) {
          var xhr;
          var js = localStorage.getItem(name);
          if (js == null || js.length == 0 || this.pageVersion != localStorage.getItem("version")) {
            if (window.ActiveXObject) {
              xhr = new ActiveXObject("Microsoft.XMLHTTP");
            } else if (window.XMLHttpRequest) {
              xhr = new XMLHttpRequest();
            }
            if (xhr != null) {
              xhr.open("GET", url);
              xhr.send(null);
              xhr.onreadystatechange = function () {
                if (xhr.readyState == 4 && xhr.status == 200) {
                  js = xhr.responseText;
                  localStorage.setItem(name, js);
                  localStorage.setItem("version", whir.res.pageVersion);
                  js = js == null ? "" : js;
                  whir.res.writeJs(js);
                  if (callback != null) {
                    callback(); //回调，执行下一个引用
                  }
                }
              };
            }
          } else {
            whir.res.writeJs(js);
              if (callback != null) {
                  callback(); //回调，执行下一个引用
              }
          }
      } else {
          whir.res.linkJs(url);
      }
  },
  loadCss: function (name, url) {//动态加载css文件并缓存
      if (window.localStorage) {
          var xhr;
          var css = localStorage.getItem(name);
          if (css == null || css.length == 0 || this.pageVersion != localStorage.getItem("version")) {
              if (window.ActiveXObject) {
                  xhr = new ActiveXObject("Microsoft.XMLHTTP");
              } else if (window.XMLHttpRequest) {
                  xhr = new XMLHttpRequest();
              }
              if (xhr != null) {
                  xhr.open("GET", url);
                  xhr.send(null);
                  xhr.onreadystatechange = function () {
                      if (xhr.readyState == 4 && xhr.status == 200) {
                          css = xhr.responseText;
                          localStorage.setItem(name, css);
                          localStorage.setItem("version", whir.res.pageVersion);
                          css = css == null ? "" : css;
                           css = css.replace(/\..\/img\//g,+"http://www.test.com/img/"); //css里的img路径需单独处理
                          whir.res.writeCss(css);
                      }
                  };
              }
          } else {
              whir.res.writeCss(css);
          }
      } else {
          whir.res.linkCss(url);
      }
  },
  //往页面写入js脚本
  writeJs: function (text) {
      var head = document.getElementsByTagName('HEAD').item(0);
      var link = document.createElement("script");
      link.type = "text/javascript";
      link.innerHTML = text;
      head.appendChild(link);
  },
  //往页面写入css样式
  writeCss: function (text) {
      var head = document.getElementsByTagName('HEAD').item(0);
      var link = document.createElement("style");
      link.type = "text/css";
      link.innerHTML = text;
      head.appendChild(link);
  },
  //往页面引入js脚本
  linkJs: function (url) {
      var head = document.getElementsByTagName('HEAD').item(0);
      var link = document.createElement("script");
      link.type = "text/javascript";
      link.src = url;
      head.appendChild(link);
  },
  //往页面引入css样式
  linkCss: function (url) {
      var head = document.getElementsByTagName('HEAD').item(0);
      var link = document.createElement("link");
      link.type = "text/css";
      link.rel = "stylesheet";
      link.rev = "stylesheet";
      link.media = "screen";
      link.href = url;
      head.appendChild(link);
  }
};
```
 
因为每当静态文件储存到了本地缓存之后，之后除非是手动删除不然就会一直存在，上面的代码在读取了一次之后，以后再次加载的时候就不会再去加载，而是直接去本地储存里读取，所以如果当css或js静态文件有改动的时候需要更改版本号，就需要手动更改`pageVersion`版本号参数

**页面加载及添加js缓存调用方法**
参数：[缓存名称，文件加载路径，回调方法(可选)]

``` javascript
whir.res.loadJs("jquery.js", "//cdn.bootcss.com/jquery/1.12.4/jquery.min.js",null);
```

还有另外一种加载情景，由于js加载有顺序要求，所以需要将后加载的脚本作为前一个脚本的回调参数传入，不然可能会出现有的js脚本需要jquery的支持，但在jquery加载之前执行了，会因为jquery未加载完而报错

```javascript
whir.res.loadJs("jquery", "//cdn.bootcss.com/jquery/1.12.4/jquery.min.js",
  function () {
    whir.res.loadJs("index.js", "script/index.js", null);
  }
);
```

**页面加载及添加css缓存调用方法**
参数：[缓存名称，文件加载路径]

```javascript
whir.res.loadCss("css", "/style/style.css", null);
```


<font color="red">注：</font>在加载网络静态文件的时候，会容易出现相对路径加载不正确的情况，例如图片、字体文件，因为加载到本地储存就相当于这个文件变成了当前网站本地的css文件，与网络文件已经无关了，但里面的相对路径也是基于当前网站进行读取文件，而网络文件是基于他自己域名进行获取的路径，所以会加载失败，因此在css文件加载完成之后需要对相对路径做特殊处理

```
//css里的img路径需单独处理
var css = "--css代码--";
css.replace(/\..\/img\//g,+"http://www.test.com/img/"); 
```

用加入本地缓存的方法把静态文件缓存到本地，可以大大的提高网站的响应速度，在网站css和js要加载很多的情况下，加入到本地缓存也是一个不错的方法，从几十个请求，可以立马变成几个请求，唯一需要注意的就是对于网络路径和第一次脚本的加载顺序、效果方面需要费心做一下处理，对于小站点和个人博客来说用这种方法挺好的




