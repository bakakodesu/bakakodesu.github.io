---
title: Mac 环境下搭建 Hexo 个人博客
date: 2023-08-28 16:15:32
categories:
- [环境配置, Hexo]
tags:
- Hexo
math: false
---

原文：[Mac OS 环境下使用 Hexo 搭建个人博客](https://l3zc.com/2022/05/mac环境下使用hexo搭建个人博客/)。

参考：

1. [Mac 系统有什么软件打开 .pub 文件？](https://www.zhihu.com/question/26071345)；
2. [hexo 发布之后 gitpage 自定义域名失效](https://blog.csdn.net/xs20691718/article/details/81873921)。

### 准备工作

#### 安装 Git 和 Node.js

```bash
brew install git
brew install node
```

#### 测试所需组件是否安装成功

```bash
npm -v
node -v
git --version
```

均返回版本号即代表安装成功。

### 安装 Hexo

我们使用 npm 命令安装 Hexo：

```bash
npm install -g hexo-cli
```

等待安装完成后初始化博客：

```bash
hexo init blog
```

这条指令的作用是在你目前所在的目录下（默认是你的 home 目录）新建一个名为 blog 的文件夹（你可以将这条命令中的 blog 替换成任何你想要的名字），并在内初始化你的博客。**之后所有的操作都在这个文件夹下进行，请确保在执行后文的操作前已经切换到了这个文件夹（cd blog）。**

待初始化完成，切换到 blog 文件夹中，即可生成和预览博客。

```bash
cd blog
hexo g
hexo s
```

执行 `hexo s` 命令后，通过浏览器访问 `http://127.0.0.1:4000` 可以预览你的博客，预览功能会实时监测博客的文件变化，并实时反馈在浏览器上，在调试博客前端时非常好用。

### 部署网站到 GitHub

执行 `hexo g` 命令后博客静态文件会生成在 public 文件夹下，只需要将这些文件上传到你的静态网站托管服务即可将站点发布到公网，本文用到的托管服务是 Github Pages，下文提到的用户名均指你的 Github 用户名。

#### 添加 SSH Key 到 github

若之前使用过相关功能且已经配置过请跳过此步。

配置 Git 用户名和账号：

```bash
git config --global user.name "username"
git config --global user.email "example@example.com"
```

其中 `username` 是你的用户名，`example@example.com` 是你 Github 的登录邮箱。

然后通过终端命令生成 SSH Key：

```bash
ssh-keygen -t rsa -C "example@example.com"
```

如果已经创建过会出现 `Overwrite (y/n)? n` 提示可以输入 `n`，没有创建过的话三个回车就好，执行完成后会在 `~/.ssh/id_rsa.pub` 目录下生成需要使用的 key，也就是公钥。

到账户主文件夹下同时按 `Command+Shift+.` 显示隐藏文件，找到 `.ssh` 文件夹，打开 `id_rsa.pub`，将里面的内容全部复制（使用 cd 命令进入 `id_rsa.pub` 目录，再使用 `more id_rsa.pub` 命令查看公钥的内容）。

打开 [GitHub_Settings_keys](https://github.com/settings/keys) 页面，新建 New SSH Key，Title 随意填，Key 就把刚才复制的内容全都粘贴上去。然后点 Add SSH Key 即可。

完成这些工作以后可以通过以下命令测试是否配置成功：

```bash
ssh git@github.com
```

出现你的 Github 用户名代表配置成功。

#### 上传本地文件到 Github

在Github上新建一个仓库，Repository Name 填 `你的用户名.github.io`，仓库状态设为 Public，点击 Create Repository 创建新的网站仓库。

安装 Hexo 的扩展 `hexo-deployer-git`，注意一定要在你的博客文件夹下执行：

```bash
npm install hexo-deployer-git --save 
```

编辑博客安装文件夹下的 `_config.yml` 文件，**翻到底部**，修改 deploy 部分为：

```yaml
deploy:
  type: git
  repo: git@github.com:你的用户名/你的用户名.github.io.git
  branch: master
```

修改完成后保存，此时就可以使用 `hexo d` 把存在本地的博客 deploy 到 Github 上了：

```bash
hexo g
hexo d
```

第一次执行 `hexo d` 命令时可能要求你输入你的 Github 账号和密码，如果报错，可能是因为 Github 在 2021 年 8 月 13 日停止了使用账户密码认证的支持，这时需要在 Gihub 上 [Developer Settings页面](https://github.com/settings/tokens) 上新建一个 Personal Access Token 代替密码就可以顺利 deploy。

执行完毕后，访问 `你的用户名.github.io` 就能看到你的博客了。

### 绑定自定义域名（可选）

其实到这一步就可以开始写博客了，默认的 github.io 的域名是可以更换的：

首先自行申请一个域名，登陆到域名申请的后台，添加一条 `CNAME` 解析记录：`你的用户名.github.io`。

打开存放博客的 Github 仓库，进入 Settings 页面在 pages 选项里的 Custom domain 里填入你的域名，点击 Save，勾选 Enforce HTTPS，即可通过自定义域名访问你的博客。

每次配置 Custom domain 之后，再次 `hexo deploy` 之后，Custom domain 会被重置失效。解决方案为：

在 hexo 生成的博客的 source 目录下新建一个 `CNAME` 文件，然后在这个文件中填入你的域名，这样就不会每次发布之后，Custom domain 都被重置掉。
