---
title: 关于hexo博客插入图片显示问题
tags: hexo
categories: hexo
abbrlink: 54406
---
# 关于hexo博客插入图片显示问题

> 踩坑日记，今天又学到新东西了。

首先
1 把主页配置文件_config.yml 里的post_asset_folder:这个选项设置为true

2 在你的hexo目录下git bush `npm install hexo-asset-image --save`，下载安装一个可以上传本地图片的插件.

3 安装完后，再运行hexo n "xxxx"来生成md博文时，/source/_posts文件夹内除了xxxx.md文件还有一个同名的文件夹(也可以手动在该目录下添加，但是md文件的title什么的要自己添加)

然后
4 最后在xxxx.md中想引入图片时，先把图片复制到xxxx这个文件夹中，然后只需要在xxxx.md中按照markdown的格式引入图片：

`![你想输入的替代文字](xxxx/图片名.jpg)`

注意： xxxx是这个md文件的名字，也是同名文件夹的名字。只需要有文件夹名字即可，不需要有什么绝对路径。你想引入的图片就只需要放入xxxx这个文件夹内就好了，很像引用相对路径。

5 最后检查一下，hexo g生成页面后，进入public\2017\02\26\index.html文件中查看相关字段，可以发现，html标签内的语句是`<img src="2017/02/26/xxxx/图片名.jpg">`，而不是`<img src="xxxx/图片名.jpg>`。这很重要，关乎你的网页是否可以真正加载你想插入的图片。

做到这可能就出问题了，解决办法如下：

由于`hexo`默认无法自动处理文章插入本地图片，需要通过扩展插件支持。

## 问题

由于hexo 3 版本后对很多插件支持有问题，[hexo-asset-image](https://github.com/CodeFalling/hexo-asset-image.git)插件在处理`data.permalink`链接时出现路径错误，把年月去掉了，导致最后生成的路径为`%d/xxx/xxx`需要对其做兼容处理。通过判断当前版本是否等于`3`的版本做不同的路径分割。

解决方法：(任意一种应该都能解决)

**1**.可直接安装已经修改过得插件`npm install https://github.com/7ym0n/hexo-asset-image --sa`。

**2**.找到node_modules 中的 hexo-asset-image，打开修改js文件，推荐跳到最后一行直接安装

在代码中加入：(推荐学习Ctrl + F 搜索快捷键快速定位)

```JS
var version = String(hexo.version).split('.');
```



修改`date.permalink`处理：

（这一步可能是不需要的）

```JS
var link = data.permalink;  
if(version.length > 0 && Number(version[0]) == 3) 
    var beginPos = getPosition(link, '/', 1) + 1; 
else 
    var beginPos = getPosition(link, '/', 3) + 1;
```

重新生成静态文件即可正确显示。



