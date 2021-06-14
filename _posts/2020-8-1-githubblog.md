---
layout: post
title: "基于Github Pages快速建立个人博客"
subtitle: "其实就是说说自己怎么建这个博客的"
date: 2020-08-01 18:32:07
author: 曦秋
header-img: "/img/stu-note-bg.jpg"
header-mask: 0.5
permalink: /post/gitblog.html
catalog: true
top: false
tags: 
    - 个人博客
    - 学习笔记
---

建站到现在差不多一个多月了，之前筹备这个博客计划也有很长时间了，自己也走了相对比较多的弯路，当然还有很多未知的东西还没有接触过，还需要继续努力。我结合我自己的经历，尽可能简洁的介绍一下自己是如何从入门小白拥有属于自己的博客的。我自己使用的是VS Code，因为这个实在是太方便了，官方还有GitHub Desktop可供使用；Linux使用git bush也可以。介绍的套话我就不多说了，我主要分为三部分：建GitHub、建域名、把内容放域名里。

用到的网站/软件：

✔ [GitHub](https://blog.atdanliu.cn/jump.html?tpurl=http://github.com/)

✔ [Freenom](https://blog.atdanliu.cn/jump.html?tpurl=http://www.freenom.com/)（这个是建域名的，也可以选择阿里云或腾讯云）

✔ [DNSPod](https://blog.atdanliu.cn/jump.html?tpurl=https://www.dnspod.cn/)

✔ [Vercel](https://blog.atdanliu.cn/jump.html?tpurl=https://www.vercel.com/)（20.08.24更新）

✔ VS Code / GitHub Desktop

<hr>

### 建GitHub

建一个GitHub账号（过程略），然后去建一个Repository，如果没有建域名的话，建议的仓库名是“自己的用户名+github.io”，这会省去很多麻烦；如果是自定义域名的话，那这个就很随意，你可以想起什么就起什么。

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubrepo.png)

接下来去找一个喜欢的模板吧，模板这个东西其实大同小异，无论是什么格式的，只要看懂框架就解决了一大半了。这里给一个[Jekyll的主题](https://blog.atdanliu.cn/jump.html?tpurl=http://jekyllthemes.org/)汇总，可以去里面进行下载或者fork。我使用的是[黄玄的模板](/about)（Hux Blog），排版很简洁，代码可读性很强。这个看个人喜好，有些人就喜欢各种动画各种功能，有些人可能更青睐简洁明了的博客，我就是属于后者的类型。

<hr>

### 建域名

这部分可以忽视，如果想要一个个性的网站域名的话，就请继续看下去；如果只是想实现博客功能，可以直接去“把内容放域名里”部分了。

目前最常见的注册域名的方式主要是在腾讯云、阿里云的比较多，其他的优质云服务例如又拍云、七牛云等等似乎并不提供注册域名的服务。但是这里面就有一个小问题：域名要钱。作为一名学生党，在没有稳定收入之前并不想在这方面投入过多，因此我选择了一种免费的注册域名的方式：[Freenom](https://blog.atdanliu.cn/jump.html?tpurl=http://www.reenom.com/)

后来才发现其实人家收费也是有好处的：最起码帮你备案了，能用国内的大牌CDN了，加载最起码不费劲，域名也好听好记。没办法……所以最后我手动CDN了，当然这是后话了。

首先进入网页，在中间输入自己想要的域名，进行查找：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomindex.png)

选择一个自己喜欢并且免费的域名：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomask.png)

我这里选择了.cf的域名，加入到购物车后进行配置，配置部分之后再说，时间比较重要：默认为3个月免费，最多可以到12个月免费，但是1年就是要收费了（这件事告诉我们：12个月 ≠ 1年）

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomtime.png)

之后需要结算，很多人建议用Google邮箱注册，其实我觉得没必要，QQ邮箱现在也支持注册了，而且那些填写的信息也不需要多么真实可靠。所以如果用谷歌账号的点右边，国内邮箱输入后点左边。

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomcheck.png)

我这边使用的国内邮箱，会收到一封确认邮件：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenommail.png)

进入链接后需要填写个人信息，这里其实没必要那么真实，主要是邮箱需要是刚才的邮箱。付款方式选择默认（右下角）：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomdetails.png)

之后回到购物车进行支付（免费），基本上就完成了。整个过程在支付的最后不建议用VPN，因为某些地区不允许购买，如果你嫌加载慢的话可以在之前一直用。在这里确认你是否购买成功：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomdomains.png)

<hr>

### 把内容放域名里

如果把主题下载下来了，使用VS Code或者GitHub Desktop把它推到新建的仓库就可以了；如果是直接fork的，在这个仓库的基础上直接修改就好。我这里主要说一下使用VS Code怎么推上去。

首先，“文件” → “打开文件夹…”，选择你的主题的文件夹，确定；之后，到左侧栏第三项，选择“初始化数据库”：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubpublic.png)

资源管理器右侧的菜单中，依次选择“全部提交” → “拉取” → “推送”即可，中间会让你输入你的GitHub账号和密码、你仓库的名字、你提交的备注等等，注意提示正确填写即可。这里我只是做个示范：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubhand.png)

推上去后可到GitHub网站上查看自己的仓库是否上传完成即可。但是此时网站还不能访问，需要进行进一步配置。选择仓库中的“Settings”，下拉到“GitHub Pages”选项，在“source”选项中选择“master”：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubpages.png)

如果没有自定义域名的，博客内容已经顺利发布了，地址是红色箭头指的；如果自定义域名了，在绿色箭头处输入刚刚建立的域名保存。

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubdomains.png)

进入DNSPod，添加域名，输入之前建好的域名进行解析；解析完成后进入：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-githubdnspod.png)

红色箭头的两个地址复制下来，同时进入freenom，在建立的域名后选择“manage domain”：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenommanage.png)

选择“Management Tools”中的“Nameservers”，将刚刚的两个地址复制进去，保存更改：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-freenomname.png)

回到DNSPod，添加两条CNAME记录，地址写到自己的仓库下：

![](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/img/in-post/gitblog/gitblog-dnspodip.png)

至此，整个自定义博客算是正式建立完成了！之后关于网站的维护全部在DNSPod就可以了，记录添加需要等待一段时间（一般不会超过8h），当能ping通自己的域名时，说明已经大功告成了！

### 博客的发布

竟然还有第四部分！博客建立了，没有博文怎么能行。因此对于没有基础的小白来说，在不考虑功能的情况下，能正常的发文章就可以了。因此简单说一下如何发布文章。

文章主要是Markdown的格式，分为头部和正文。头部主要是标题、作者、日期等等信息，还可能有背景图片、文章置顶、辅助目录等等功能，取决于你使用的模板了。如果你看不懂源码，那你可以只使用这几个标签，剩下的标签根据模板与添加的功能而定。能者多劳，效果自然更好：

&nbsp;&nbsp;&nbsp;&nbsp;✡ “layout”（一般博文选择post，功能页选择default，视模板而定）

&nbsp;&nbsp;&nbsp;&nbsp;✡ “题目”（title）

&nbsp;&nbsp;&nbsp;&nbsp;✡ “日期”（date）

&nbsp;&nbsp;&nbsp;&nbsp;✡ “作者”（author）

正文部分可以参考HTML的格式，基本上HTML能用的在这里都能用。

如果你还是一头雾水，你可以参考一下我给你的这个[范例](http://cdn.jsdelivr.net/gh/DanLCJ/danliublog@master/_posts/test.markdown)。

<hr>

### 20.08.11更新

原域名连续两天主服务器在流量高峰期宕机，DNSPod的免费版实在是不中用。忍不了的情况下还是去腾讯云注册了个域名，就是现在使用的atdanliu.cn，没啥大动静的话应该不会再动了。这几天电信还在系统维护，还得等着维护结束才能域名备案，之后争取接进七牛云的CDN，网络的维护就算告一段落了。

### 20.08.23更新

放弃备案了：8.17ICP备案系统维护完毕，相当于是个大更新，ICP备案官网根本就没有申请备案的入口；给电信的营业厅打电话，不仅态度不好而且也没提供任何材料。之前选择备案只是因为可以白嫖七牛云的免费云存储和CDN，但是原先博客的国内加载速度，打开网站差不多10s，再要加载脚本结束估计快20s了，对于用户来说体验极其糟糕。然而我看到了[DotIN13的博客](https://blog.atdanliu.cn/jump.html?tpurl=https://www.wannaexpresso.com/2020/03/14/move-to-zeit/)之后决定迁移到了[Vercel](https://blog.atdanliu.cn/jump.html?tpurl=https://vercel.com)，遇到的问题也和DotIN13遇到的问题一模一样，所以解决起来非常方便。搭建完成后我的网络ping大约40-50ms，这是个非常理想的速度了。和之前DNSPod + Github Pages相比，稳定性和速度都有非常卓越的提升。我之后会补充如何迁移到Vercel上的。另外，我的sitemap提交到Google上，两天之后就能搜索到；提交到百度那边半个月都没收录，快要气死了气死了气死了……