---
title: cocoapods 更新失败的解决办法
date: 2023-01-09 17:45:44
categories:
- [环境配置, Xocde]
tags:
- iOS Development
- Mac
math: false
---

最近要写 swift 代码，需要用到第三方库，所以需要更新 cocoapods

输入

```sh
sudo gem install cocoapods
```

更新失败，出现：

```sh
Ignoring ffi-1.15.0 because its extensions are not built. Try: gem pristine ffi --version 1.15.0
Fetching rexml-3.2.6.gem
Fetching xcodeproj-1.23.0.gem
Fetching ruby-macho-2.5.1.gem
Fetching molinillo-0.8.0.gem
Fetching cocoapods-trunk-1.6.0.gem
Fetching cocoapods-downloader-2.1.gem
Fetching public_suffix-4.0.7.gem
Fetching concurrent-ruby-1.2.2.gem
Fetching addressable-2.8.6.gem
Fetching cocoapods-core-1.14.3.gem
Fetching cocoapods-1.14.3.gem
Successfully installed rexml-3.2.6
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /usr/bin directory.
```

此时我以为是 gem 版本太低，随即更新 gem，输入

```sh
sudo gem update --system -n /usr/local/bin
```

更新失败，出现：

```sh
Ignoring ffi-1.15.0 because its extensions are not built. Try: gem pristine ffi --version 1.15.0
Updating rubygems-update
ERROR:  Error installing rubygems-update:
	rubygems-update-3.5.4 requires Ruby version >= 3.0.0. The current ruby version is 2.6.10.210.
Installing RubyGems 3.2.15
Ignoring ffi-1.15.0 because its extensions are not built. Try: gem pristine ffi --version 1.15.0
ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted @ rb_sysopen - /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/bin/gem
```

我以为是 Ruby 版本过低，于是更新 Ruby，输入

```sh
brew install ruby
```

更新成功，但是 `sudo gem install cocoapods` 或 `sudo gem update --system -n /usr/local/bin` 时，报错不变。

我又输入提示指令

```sh
gem pristine ffi --version 1.15.0
```

但是不生效

```sh
Ignoring ffi-1.15.0 because its extensions are not built. Try: gem pristine ffi --version 1.15.0
Restoring gems to pristine condition...
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

最后在

[Ignoring ffi-1.13.1 because its extensions are not built. Try: gem pristine ffi --version 1.13.1](https://stackoverflow.com/questions/64698820/ignoring-ffi-1-13-1-because-its-extensions-are-not-built-try-gem-pristine-ffi)

中找到解决办法。按照高赞回答说的，我先输入了

```sh
brew install cocoapods
```

但是提示

```sh
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink bin/pod
Target /usr/local/bin/pod
already exists. You may want to remove it:
  rm '/usr/local/bin/pod'

To force the link and overwrite all conflicting files:
  brew link --overwrite cocoapods

To list all files that would be deleted:
  brew link --overwrite --dry-run cocoapods

Possible conflicting files are:
/usr/local/bin/pod
/usr/local/bin/xcodeproj
==> Summary
🍺  /usr/local/Cellar/cocoapods/1.14.3: 13,791 files, 28.8MB
==> Running `brew cleanup cocoapods`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
```

所以按照回答以及提示，我输入

```sh
brew link --overwrite cocoapods
```

之后再输入 `brew install cocoapods` 自此，cocoapods 更新成功
