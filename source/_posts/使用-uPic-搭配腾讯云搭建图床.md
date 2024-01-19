---
title: 使用 uPic 搭配腾讯云搭建图床
date: 2023-08-26 10:54:30
categories:
- [环境配置, 图床]
tags:
- Hexo
math: false
---

[原文链接](https://sspai.com/post/64169)

### 搭建私有图床

免费图床有两个缺点，一方面是基于隐私的保护的问题，另一方面是你不知道它什么时候可能就没了~，所以如果你对资源管理有更高的要求，或者你是一个小团队需要有自己的图床以保证稳定性，不妨往下看，我手把手教你搭建自己的私有图床。

需要用到两个东西：

1. 腾讯云 COS（对象存储）
2. uPic 客户端

#### 购买腾讯云 COS

其实国内提供对象存储服务的有很多，例如腾讯云、阿里云、七牛云、百度云、又拍云等，选择腾讯云主要是基于两点考虑：

1. 稳定可靠速度快，节点丰富还便宜
2. 支持微信或 QQ 登录，对大多数人来说方面，不用再格外注册账号

> 你甚至可以使用 github 来做免费图床，但缺点是 github 在国内速度太慢了，体验不好，我不推荐。

首先，你需要注册并开通「[腾讯云对象存储](https://sspai.com/link?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fconsole.cloud.tencent.com%2Fcos5)」，你可以使用微信或 QQ 登录：

![img](https://cdn.sspai.com/2020/12/22/8c71da4b83b79821350aeb074312bb85.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

在「存储桶列表」>「创建存储桶」，地区按照你的实际情况选择，访问权限设置为「公有读私有写」：

![img](https://cdn.sspai.com/2020/12/22/157a4689ac564d86ab64cfe8ce0c456b.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

![img](https://cdn.sspai.com/2020/12/22/16e922b14f5ae75c91845683a540b870.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

存储桶创建好之后，接下来就可以直接上传图片：

![img](https://cdn.sspai.com/2020/12/22/b09b74e4a9cd77b4255cd4542d519ba9.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

上传完成后，点击「详情」，复制「对象地址」中的 URL 即可访问对象：

![img](https://cdn.sspai.com/2020/12/22/e6bd74b237fa8914f5dc85788c8b3fdf.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

![img](https://cdn.sspai.com/2020/12/22/5f6286198ba8b1ad6c743952cd4d9b99.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

如果你要使用 Markdown，只需要手动编辑 Markdown 语法即可，例如：

```markdown
![example](https://myupic-1253463441.cos.ap-nanjing.myqcloud.com/3b6cae2afdea8f710c0a5ccb460c39cb-0.jpg)
```

腾讯云是根据流量来实时计费的，根据我自己的使用经验来看价格是非常便宜：

![img](https://cdn.sspai.com/2020/12/22/3908682f470e049dae15ff3b3178352d.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

更详细的计费规则参见：[腾讯云 COS 定价](https://sspai.com/link?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fcloud.tencent.com%2Fproduct%2Fcos%2Fpricing)

上面已经介绍了基本的使用方法，但是你可能也发现了，如果每张图片都这样手动上传、手动复制链接、手动粘贴的话，效率未免太低了，这时候就需要使用我们的主角—— uPic 客户端了。

#### uPic 客户端配置

uPic客户端是开源免费的，可以在如下 GitHub 地址进行下载：

- [https://github.com/gee1k/uPic](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Fgee1k%2FuPic)

打开偏好设置，在「图床」中添加一个「腾讯云 COS」：

![img](https://cdn.sspai.com/2020/12/22/16763a27c4f28b8a2608574f1b763bd9.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

### 配置项说明

- `区域`: 对象储存空间所属的区域，可在腾讯云控制台查看。
- `空间名称`: 对象储存空间名称，可在腾讯云控制台查看。
- `SecretId`: 密钥。
- `SecretKey`: 密钥。
- `域名`: 使用腾讯云默认提供的测试域名或者你的自定义域名。域名可在控制台查看。`域名需以http://或者https://开头`。
- `保存路径`: 文件储存的路径（包括文件夹）。 `支持 {year} {month} {day} {hour} {minute} {second} {since_second} {since_millisecond} {random} {filename} {.suffix} 等变量。比如：上传的图片为 uPic.jpg，设定为 “uPic/{filename}{.suffix}”，则会保存到 “uPic/uPic.jpg”。`
- 在`保存路径`输入框后面的是`网址后缀`: 用于自定义图片处理。腾讯云 COS 目前还不支持，先预留。

**看起来有点复杂，不过没关系，下面跟着我一步步配置即可。**

点击腾讯云**「**存储桶列表**」**，点击「配置管理」：

![img](https://cdn.sspai.com/2020/12/22/1b8680f5e02cc2d70f1a0253afd037c1.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

可以看到「存储桶名称」和「访问域名」，复制填写到 uPic 中：

![img](https://cdn.sspai.com/2020/12/22/a3fc05d6a35b9065478cc417c0a26679.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

然进入腾讯云「API 密钥管理」，系统会提示建议创建子账号来管理，点击「切换使用子账号密钥」：

![img](https://cdn.sspai.com/2020/12/22/ccd164a592c4ef6659d8231145681902.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

选择「快速创建」：

![img](https://cdn.sspai.com/2020/12/22/e39f339ac3902bc03f3b8672c1e0470a.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

随意输入用户名，例如 GuestforuPic，点击用户权限：

![img](https://cdn.sspai.com/2020/12/22/461469f544f6e3037721265a3ea20757.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

搜索「QcloudCOSFullAccess」，勾选并确定：

![img](https://cdn.sspai.com/2020/12/22/71787d4c7210fceaaddf58a8805fdca9.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

![img](https://cdn.sspai.com/2020/12/22/c0856f11a2315a401d5e8dcc5ba1617f.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

然后进入腾讯云的「用户列表」>「用户详情」>「API密钥」>「新建密钥」：

![img](https://cdn.sspai.com/2020/12/22/641b4ba0223a8225ad0161ddbdb92b91.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

![img](https://cdn.sspai.com/2020/12/22/e20dabd582fda5b2ebb904738c6529b6.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

复制 SecretId 和 SecretKey 到 uPic 客户端：

![img](https://cdn.sspai.com/2020/12/22/b6c2c37042dc1bef7ec884cc3be20fde.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

至于最后的「保存路径」可以保持不变，如果你有特殊要求再做自定义。

点击「验证」测试一下，如果提示上传成功就配置完成啦！点击保存即可。

![img](https://cdn.sspai.com/2020/12/22/67ca93666ebccc3e530b3affabcbe7c6.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)
