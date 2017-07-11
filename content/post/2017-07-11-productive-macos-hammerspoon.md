---
title: "打造 macOS 的生产力环境 - Hammerspoon"
date: 2017-07-11T07:46:23+08:00
slug: "Productive macOS Hammerspoon"
draft: true
---

## 起因

MacBook Pro 加外接显示器现在差不多是开发的标配了。用外接显示器爽是爽，但作为一个只用 Trackpad 没有鼠标的用户，每次在不同屏幕间切换光标，手指至少要在 Trackpad 移动一半的距离，效率非常差。搜了一下，有 Sizeup 这种商业软件有这个功能，但价格 12.99 刀，真是不便宜。接着搜看到有人用 Hammerspoon 做窗口管理的文章，感觉用这个应该可以实现，就找资料研究了一下。

## 介绍

Hammerspoon 是 macOS 上一个强大的自动化工具，是一款开源软件，但安装之后 Hammerspoon 默认什么功能也没有，所有的功能都在 Lua 脚本中，需要用户自己编写。Hammerspoon 可以让用户通过 Lua 脚本直接调用 macOS 提供的 API，能做的事情既有自定义快捷键这种简单操作，也能实现连上家里 Wi-Fi 后自动打开某视频网站、到办公室后自动静音等复杂功能。官方提供的 API 已经非常丰富，包括管理应用程序、管理系统音频设备、画图、网格化窗口等，详细了解可以直接看[官方的 API](http://www.hammerspoon.org/docs/)。

## 安装配置

可以直接到[官方](http://www.hammerspoon.org/)下载安装，用 `Homebrew-Cask` 的用户也可以直接在命令行安装 `brew cask install hammerspoon`。启动 Hammerspoon 后，程序会自动加载配置，配置文件位于 `~/.hammerspoon/init.lua`。可以在设置中取消显示 Dock ICON 的选项，这样 Hammerspoon 的图标就不会显示在 Dock 上。

## 使用

[官方文档](http://www.hammerspoon.org/docs/index.html)写的很细致，要好好了解的可以直接去看。这里只用一个经典的 `Hello World` 程序感受一下 Hammerspoon 的简单。

`vi ~/.hammerspoon/init.lua` 添加以下内容，然后重新加载配置文件，就可以使用快捷键  `Control + Alt + H` 在通知中心弹出一个 `Hello Hammerspoon!` 的通知，就是这么简单。

```
hs.hotkey.bind({"alt", "ctrl"}, "H", function()
  hs.notify.new({title="Hammerspoon", informativeText="Hello Hammerspoon!"}):send()
end)
```

## [我的配置](https://github.com/greyby/hammerspoon)

如果所有的代码都放到 `init.lua` 下面，随着功能增多，这个文件会越来越大，修改起来不方便。这里把所有功能按模块 `modules` 下面，具体有以下模块：

|   模块       |  功能           |
| ----------- | -------------- |
| hotkey.lua  | 快捷键配置       |
| reload.lua  | 重新加载配置文件  |
| windows.lua | 窗口管理     |
| launcher.lua| 程序快速启动  |
| system.lua  | 系统工具     |


包含的功能如下


### 窗口管理

在不同屏幕间切换光标，这个也是我的最初需求。这个模块里面包括全屏、最大化窗口、窗口分割、窗口移动和光标移动等。

### 快速启动

可以通过快捷键直接启动或者切换程序。

### 系统工具

锁屏


## 推荐

[https://github.com/ashfinal/awesome-hammerspoon](https://github.com/ashfinal/awesome-hammerspoon)

这个项目可以说展示了 Hammerspoon 的强大，里面有桌面小工具、搜索、番茄时钟等很多功能。可以去围观开下脑洞。
