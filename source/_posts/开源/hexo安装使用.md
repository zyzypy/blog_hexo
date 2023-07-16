---
title: Hexo 安装与使用
comments: true
date: 2018-03-28 10:35:36
updated: 2018-06-07 19:08:00
tags:
- hexo
- 开源
categories:
- 开源
permalink:
---

### 常用命令
运行服务器 `hexo serve`  
新建文章 `hexo new post [article name]`  



### 文章文件命名
目录 source/_posts 
考虑到文章较多
_posts目录下又分了几个目录，发现生成后端url为域名/posts下的目录/category，前端改变不大，后端按内容分目录（20180729迁移）。
命名 大类_文章 （英文）
例如 tools_vmware-fution-10
具体标题md文章内改title配置
例如 VmwareFusion10的安装与使用


### hexo 安装
`cnpm install hexo-cli -g`  
`cd [program dir]`  
`cnpm install`  
#### 坑
win与mac的node_modules不通用，需要重新安装依赖包

### 运行
`hexo serve`  
支持热加载
#### 坑
时间长不动可能会报错；修改配置文件可能不生效
需要重启服务

### 生成静态文件
`hexo generate` 或 `hexo g`
使用后生成public文件夹，含有html js 文章内容，即 *纯静态*类博客，点击index.html访问，部署时可以将这个文件夹放到vps
### 部署
`hexo deploy` 或 `hexo d`  
使用前需要配置，支持github和vps  
可以观察到这个命令是先generate再上传文件到远程目录，如果经常更新文章的话简易配置一下一劳永逸，也可以吧public文件夹上传到vps再用web服务器代理
### 清除
`hexo clean`
改配置不生效；generate不生效。一些疑难杂症是由于缓存和本身的数据库,运行此命令可解决。
### 主题
官网theme列表不全，可以去github搜索
有几个主题挺漂亮，但没有实现category、tags、翻页、全局搜索这些东西
精挑细选出的一个主题 hexo-theme-icarus  
#### 激活category tags
复制/themes/icarus/_source 到 /source/下
主题_config.yml 取消相关注释
#### banner thumbnail
文章头部formatter中添加  
banner: cover.jpg
thumbnail: 
thumbnail可以省略
#### 百度统计
主题下config
baidu_analytics: # enter Baidu Analytics hash key
百度统计管理后台/获取统计代码 ` hm.src = "https://hm.baidu.com/hm.js?05945971757165c93bca4e5a2e5xxxxx"` 填入?号后面的参数

### 图片引用
#### 坑
- *路径解析，会自动到 文章同名资源目录下搜索。* 比如xxx.md，路径就是 http://localhost/2018/03/01/xxx/cover.jpg .  
如果写成 xxx/cover.jpg  ./xxx/cover/jpg 等相对路径是显示不出来的。
比较稳妥的方式是引用绝对路径或cdn图片，但绝对路径需要在开发和正式域名上切换，cdn不可控，还是喜欢引用本地的方式。
- 有时浏览器的url是跟路径，所以不会触发图片路由，但文章由于缓存还正常显示文章，让人以为图片路径写错了。
- 有个插件自动根据域名补成绝对路径，没试过。

### 特殊字符
- `{% raw %} {{ }} {% endraw %}` 会被解析为值引用导致报错，使用{% raw %} {% raw %}raw endraw{% endraw %} {% endraw %}标签括住。


### 插件
#### sftp
VPS比如阿里云默认开启SSH，又比FTP安全，配置差不多，故采用。
官网的ssh插件没有翻译，只有在英文版下的文档才有，估计很多人没发现，github的star数量也很小。操作类似jetbrain系列ide的部署中的sftp，指定
服务器地址、端口号、用户名、密码或密钥对。主意vps是否放通端口，非root用户是否有远程目录写权限，root用户是否在ssh_config配置中被禁止登陆，
可以先用ssh登陆工具试一下，没什么问题的话再配置目录。配置成功后`hexo deploy`
#### 全局搜索
#### emoji渲染
方式一：替换默认渲染器hexo-renderer-marked，安装支持插件的渲染器hexo-renderer-markdown-it，再安装这个渲染器的插件markdown-it-emoji，
再配置。只是想添加个表情，被吓到了。
[Hexo添加emoji](http://very9s.net/post/hexo-support-emoji/#%E4%BD%BF%E7%94%A8markdown-it%E6%B8%B2%E6%9F%93)
[markdown-it](https://github.com/markdown-it/markdown-it)
[hexo-renderer-markdown-it](https://github.com/hexojs/hexo-renderer-markdown-it)
方式二：查出emoji对应的html代码
[Emoji Unicode Tables](https://apps.timwhitlock.info/emoji/tables/unicode)
方式三：表情图包放到 项目/source/images 公用图片目录下再引用
方式四：hexo-filter-github-emojis, hexo官网插件列表搜索emoji时发现的，简单的安装和表情代码映射到github的表情图cdn上，看上去正是想要的
[hexo-filter-github-emojis](https://github.com/crimx/hexo-filter-github-emojis)
`cnpm install hexo-filter-github-emojis --save`
```yaml
# 项目下 _config.yml
githubEmojis:
  enable: true
  className: github-emoji
  unicode: false
  styles:
  localEmojis:
```
重启服务，成功 :smile: `:smile`

```nodejs
npm un hexo-renderer-marked --save
npm i hexo-renderer-markdown-it --save
```
#### stly渲染

