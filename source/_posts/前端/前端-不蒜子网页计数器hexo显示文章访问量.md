---
title: 软件_不蒜子网页计数器hexo显示文章访问量
comments: true
toc: false
date: 2018-04-03 22:45:34
updated:
tags:
- 网页计数
- hexo
categories:
- 前端
banner: 不蒜子1.png
---

## referance
[不蒜子官网](http://busuanzi.ibruce.info/)  
[不如的博客-不蒜子](http://ibruce.info/2015/04/04/busuanzi/) 作者博客教程


## 实际操作
1. 引用js  
```javascript
<script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```
async是新属性，现代浏览器支持异步加载，来获得更好的浏览体验  
存放位置，项目全局加载js的地方，比如说header或footer  
我在footer中添加,修改如下：
myhexo/themes/icaus/layout/common/footer.ejs  
```ejs
<footer id="footer">
    <div class="outer">
        <div id="footer-info" class="inner">
            <!--不蒜子统计-->
            <script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
            <span id="busuanzi_container_site_pv">site_pv:<span id="busuanzi_value_site_pv"></span>&nbsp;site_uv:<span id="busuanzi_value_site_uv"></span></span><br>
            &copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>&nbsp;豫ICP备16021043号-2<br>
            <!--Powered by <a href="http://hexo.io/" target="_blank">Hexo</a>. Theme by <a href="http://github.com/ppoffice">PPOffice</a>-->
            <!--Powered by <a href="http://hexo.io/" target="_blank">Hexo</a>. Theme by <a href="https://github.com/ppoffice/hexo-theme-icarus" target="_blank">hexo-theme-icarus</a>-->
            Powered by <a href="http://hexo.io/" target="_blank">Hexo</a>. Theme by hexo-theme-icarus
        </div>
    </div>
</footer>
```

2. 标签渲染
```ejs
<span id="busuanzi_value_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
<span id="busuanzi_value_site_uv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
<span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
<span id="busuanzi_container_site_uv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
<span id="busuanzi_container_page_pv">当前页面访问量<span id="busuanzi_value_site_pv"></span>次</span>
<span id="busuanzi_container_page_uv">当前页面访问量<span id="busuanzi_value_site_pv"></span>次</span>
```
- id控制渲染：靠id来控制主要是站点总访问量site和当前页面阅读量page  
- pv和uv：pv每次刷新加一，也就是1次；uv一个人看这个网站多个文章算一次，也就是1人。根据自身需要选用。我站点用的uv，文章阅读用的pv。
坑：作者博客的busuanzi_container_site_pv无法显示数据，busuanzi_value_site_pv有数据。但前者的命名方式跟当前页面id风格一致。只看了测试环境，
可能生产环境busuanzi_container_site_pv是有数据的  

我想在文章标题下，靠着日期目录旁边显示，修改如下：  
myhexo/themes/icaus/layout/common/post/artical.ejs  
```ejs
<article id="<%= post.layout %>-<%= post.slug %>" class="article article-type-<%= post.layout %><%= (post.direction && post.direction.toLowerCase() === 'rtl' ? ' rtl' : '') %>" itemscope itemprop="blogPost">
    <div class="article-inner">
        <% if (post.banner) { %>
            <%- partial('post/banner') %>
        <% } %>
        <% if (post.link || post.title) { %>
            <header class="article-header">
                <%- partial('post/title', { class_name: 'article-title' }) %>
                <% if (post.layout != 'page') { %>
                    <div class="article-meta">
                        <%- partial('post/date', { class_name: 'article-date', date_format: null }) %>
                        <%- partial('post/category') %>
                        <%- partial('post/tag') %>
                        <!--不蒜子 start-->
                        <i class="fa fa-eye"></i>
                        <span id="busuanzi_container_page_pv">
                          <span id="busuanzi_value_page_pv"></span>
                        </span>
                        <!--不蒜子 end -->
                    </div>
                <% } %>
            </header>
        <% } %>
        <%- partial('post/gallery') %>
        <div class="article-entry" itemprop="articleBody">
        
```


## 原理猜想
不用注册，没有用户appid，为什么简单的引用js后就能显示出访问量呢？后来思考如下：

- 设计：  
一张表，主要字段：域名domain、完整的请求地址url、访问量number  

- 工作过程：  
每次加载js时会请求服务器，参数为url  
数据表新建一条记录，记录url，number为1  
完整的url split by ':'分成两部分，前面的是domain  
如果url已存在，number累加1
- 查询：  
select sum(number) as 站点总次数 from table where domain= ；  
select number as 当前有页面访问次数 from table where url= ;  
- 渲染：
js将服务器返回的number放到相应id的标签中

- 定义初始值
如果博客已经运营了一段时间，可以登录不蒜子后台设置初始访问量。不蒜子正在开发的后台就是一个管理后台，可以对这张表写，来定义初始值
  
- 有人说统计不准
刚用上不蒜子的时候，我的博客本地开发时显示网页uv83万多，pv200多w！网上有些人说不准确，其实正如上面猜想的那样，因为所有用不蒜子的人，
都会写表中domain为"localhost"的记录，查询出来的就多了。  
看博客线上的访问量，显示几十，刚放上去几天的小站，应该是正确✔️的。不蒜子还是挺准的😀  
所以证实了上面工作原理的猜想。

- 作者的成本有多少
假设100w用户，每个用户有博客页面100页，在数据库表中100行，算上localhost，数据库表2亿行。不过没有外键，update和insert效率应该比较高。
当然我想的比较简单，作者为了完善产品肯定做了不少工作来解决性能问题。


## license
原创文章 欢迎转载
