---
layout: post
title: "利用JQuery实现可隐藏内容"
subtitle: "通过临时隐藏内容增加一定交互性……吧"
date: 2020-06-13 21:42:30
author: 曦秋
header-img: "/img/stu-note-bg.jpg"
permalink: /post/divhidden.html
header-mask: 0.5
catalog: false
top: false
tags: 
    - JQuery
    - HTML
    - 个人博客
    - 学习笔记
---

在html中我们经常需要把某些元素隐藏起来，有时候我们需要临时隐藏页面，让其他元素显示，来完成操作。有时候有些信息我们需要传给js，但是又不想给用户看，所以也会隐藏起来。

在html中有这两个方法都可以隐藏元素：`display`和`visibility`

直接上代码：

``` html
<head>
    <script>
        function isHidden(oDiv){
            var vDiv = document.getElementById(oDiv);
            vDiv.style.display = (vDiv.style.display == 'none')?'block':'none';
        }
    </script>
</head>

<body>
    <div style="cursor:hand" onclick="isHidden('div1')（点击这里查看隐藏内容）</div>
    <div id="div1" style="display:none"> 这个就是隐藏内容 </div>
</body>
```

这里面`display == 'none'`可以改成`visibility: hidden`，如果使用`visibility`需要显示时，使用`visibility: visible`

第一种`display`的方法是隐藏的更彻底一点。也就是说隐藏之后的元素在页面上不占空间了。排版的元素会依次移动把之前元素所占空间全部用掉。第二种`visibility`就仅仅是不可见了。但是页面上所占的位置还是它自己的，不会被其他元素所用掉。

这两种方法都可以被JQuery选择器所选择。

> <div style="cursor:hand" onclick="isHidden('div1')">e.g. 这是 display 的隐藏形式 </div>

<div id="div1" style="display:none"> 这个就是 display 的隐藏内容</div>

<!--
> <div style="cursor:hand" onclick="isHidden1('div2')">e.g. 这是 visibility 的隐藏形式 </div>

<div id="div2" style="visibility:hidden"> 这个就是 visibility 的隐藏内容</div>
-->