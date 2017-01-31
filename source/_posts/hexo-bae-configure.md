title: 放弃github pages,把hexo博客部署到百度BAE吧
date: 2017-01-31 10:10:19
tags:
- hexo
- code
- BAE
categories:
- code
---
<blockquote class="blockquote-center">
让你的博客访问速度 飞起来
</blockquote>

<!--more-->

## 博客部署到Github Pages的问题

在这之前我的博客一直都部署github pages下面.好处主要是因为

* 免费
* 部署起来方便快速.

**但是问题却有很多比如**:

* 访问速度略慢
* 不稳定 , 经常访问超时
* 不稳定造成 不利于seo 甚至百度不收录

## 解决办法 - 部署到 BAE

### BAE的介绍与配置
> [BAE官方介绍](https://cloud.baidu.com/product/bae.html) 简单来说BAE 可以提供一个支持git代码托管的静态服务器,每天只要一毛钱
当前这篇博客就托管在bae下, 5Mb/s的速度绝对够用, 速度嗖嗖嗖.
 
#### 创建一个BAE项目
* 登录 [百度云控制台](https://console.bce.baidu.com)

* 开通BAE
![开通BAE](http://cdn.netpi.me/blog/2r8ou.jpg?imageslim)

* 创建一个bae项目
![创建一个bae项目](http://cdn.netpi.me/blog/j72xf.jpg?imageslim)

* 选择light-static | **这一步要注意,代码版本工具要选择git不要选svn**
![选择light-static](http://cdn.netpi.me/blog/apbxi.jpg?imageslim)

#### 开启自动发布

* 回到部署列表 点击项目名称
![](http://cdn.netpi.me/blog/uaoke.jpg?imageslim)
* 点击发布设置
![](http://cdn.netpi.me/blog/bf3ow.jpg?imageslim)
* 开启自动发不
![](http://cdn.netpi.me/blog/eudkp.jpg?imageslim)

### 安装 `hexo-deployer-git`

`npm install hexo-deployer-git --save`

```sh
➜  hexo-bae-demo git:(master) npm install hexo-deployer-git --save
hexo-site@0.0.0 /Users/night/code/videoplusplus/github/hexo-demo/hexo-bae-demo
└── hexo-deployer-git@0.2.0
```
### 配置  _config.yml
编辑根目录下 _config.yml 添加
```sh
deploy: 
  type: git
  repo: https://git.duapp.com/appid970bs422df 
  # 这是你bae项目中的git地址 获取方法如下图
```
![](http://cdn.netpi.me/blog/hds4g.jpg?imageslim)

### 部署到BAE 
`$ hexo d` 直接部署到BAE
> 第一次执行需要输入`帐号密码` - 分别是你[百度云](https://cloud.baidu.com/)的`帐号密码`

```sh
➜  hexo-bae-demo git:(master) ✗ hexo d
    INFO  Deploying: git
    INFO  Clearing .deploy_git folder...
    INFO  Copying files from public folder...
    On branch master
    nothing to commit, working tree clean
    To https://git.duapp.com/appid970bs422df
    + d409e0c...af1773c HEAD -> master (forced update)
    Branch master set up to track remote branch master from https://git.duapp.com/appid970bs422df.
    INFO  Deploy done: git
```
这一步中如果是以上结果那么恭喜你成功了

**如果是以下结果**
```sh
➜  hexo-bae-demo git:(master) ✗ hexo d
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
On branch master
nothing to commit, working tree clean
error: RPC failed; HTTP 411 curl 22 The requested URL returned error: 411 Length Required
fatal: The remote end hung up unexpectedly
fatal: The remote end hung up unexpectedly
```
**原因是** : 文件内容过大

解决方案如下 :
* 编辑 `.deploy_git/.git/config`
* 尾部加入如下代码
```sh
[http]
	postBuffer = 524288000
```

修改之后再执行 `hexo d` 问题解决
```sh
➜  hexo-bae-demo git:(master) ✗ vim .deploy_git/.git/config
➜  hexo-bae-demo git:(master) ✗ hexo d
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
On branch master
nothing to commit, working tree clean
To https://git.duapp.com/appid970bs422df
   af1773c..6f2e504  HEAD -> master
Branch master set up to track remote branch master from https://git.duapp.com/appid970bs422df.
INFO  Deploy done: git
```

### 查看结果

![](http://cdn.netpi.me/blog/awvbm.jpg?imageslim)


## License

> 注 ：转载 请注明出处 [http://www.netpi.me/code/hexo-bae-configure/](http://www.netpi.me/code/hexo-bae-configure/)

## 感谢

> 如果这篇文章对你有帮助,请点击最上方广告一次 谢谢! 
