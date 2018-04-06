---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: IOS套件管理-CocoaPods 以及 Swift package manager(SPM) 兩種方式介紹
comments: true

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  介紹IOS的套件管理工具，CocoaPods 以及 Swift Package Manager（SPM）

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/default.jpg

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [IOS]
tags: [example, content]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

CocoaPods以ios有名的圖表視覺化Package - [Charts]為例。

Swift Package Manager以Package - [Vapor]為例。

原本想說應該都用Charts這Package做展示的
不過我實際在SPM安裝Charts，有點問題。文檔上也沒寫到如何使用SPM安裝

前言：為什麼會寫這文章呢？因為Swift更動的速度太快，
網路上中文的ios套件管理工具，有的都已經有點舊了，不適用了。

，剛好最近有機會做ios的案子，因此寫一下來分享，有問題的話也歡迎反應！


在ios中有3個主流的套件管理工具
以下來短評（極主觀
- CocoaPods
  - 較為主流的方式
  - 我認為最簡單的套件管理方式
- Swift Package Manager
  - 官方支持
  - 支援較少
  - 會遇到的問題很多
- Carthage
  - 我沒用過，不評論。

>那至於要用哪一種方式作為套件管理工具呢？<br>
>我的建議是你應該要想清楚你要使用哪些套件，<br>
>然後去每個套件的github文檔中，<br>
>看看每個Package支援哪些套件管理的工具。<br>
{:.message}


建議找每個package都支援的套件管理工具，會省去你很多麻煩。<br>
Swift每年都在更新，要找到Package支援新版本的Swift其實不容易，<br>
然而又要找到支援同樣的套件管理工具，真的難上加難。<br>

首先看一下[Charts]的github page
其實他是支持CocoaPods以及Carthage
接下來進入正文


# CocoaPods
首先我們先創建一個Single View的App

之後我們命名為**test_pod**

打開ViewController.swift

我們可以先天真的import charts看他有沒有反應，

>import Charts
{:.message}

理所當然地會出現下面的畫面

>No such module 'Charts'
{:.message}

![](/assets/img/2018-04-06-IOS-package-manager/pod/charts_cannot_import.png)

那我們要來解決這個問題，就是讓package可以使用Pod正確安裝！！

第一步為打開你的command

切換到剛剛的資料夾
>cd test_Pod
{:message}



輸入下面一行，創建好Pod所需的環境
>pod init
>open -a Xcode Podfile
{:message}

接下來會出現一個文字編輯器，有以下的程式碼區塊
![](/assets/img/2018-04-06-IOS-package-manager/pod/pod_empty.png)

那基本上只要在第7行開始

輸入你要安裝的package

像我們要安裝的是Charts

所以我們只要輸入

>pod 'Charts'
{:message}

完成後如下圖

![](/assets/img/2018-04-06-IOS-package-manager/pod/pod_write_charts.png)

之後輸入
>pod install
{:message}

完成後的圖如下

![](/assets/img/2018-04-06-IOS-package-manager/pod/pod_command.png)

之後會發現多了一個檔案 **test_Pod.xcworkspace**
CocoaPod 安裝Package的特色是會產生一個.xcworkspace

![](/assets/img/2018-04-06-IOS-package-manager/pod/xcworkspace.png)


之後都要開啟這檔案，他就會自動幫你link 

開啟後要先build一次

這樣你import後就不會出現錯誤訊息囉～

![](/assets/img/2018-04-06-IOS-package-manager/pod/charts_can_import.png)




# Swift Package Manager


輸入下面一行，創建好SPM所需的環境
>swift package init --type executable
{:message}

那我們可以看到其實多了一些東西，

下面不一一介紹，我們只要知道最重要的是 **Package.swift**

我們將它透過文字編輯器來看

~~~swift
// swift-tools-version:4.0
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "test_SPM",
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "test_SPM",
            dependencies: []),
    ]
)
~~~

簡單介紹一下，首先第一行的
是說這個這些package所使用的Swift版本。
> // swift-tools-version:4.0 <br>
{:.message}



重點是第10行的
package url > 之後會帶入該package之url 
from > 版本號碼（（其實也沒這麼簡單拉，這邊先這樣說明。
>         // .package(url: /* package url */, from: "1.0.0"),
{:.message}


以及第17行的
>            dependencies: []),
{:.message}

接下來會展示怎麼將Charts這個package套用到專案中
首先我們進入該專案的頁面[Charts]
之後點擊上方的release
如果你找不到，那就點擊這個連結吧[Charts_releases]

可以看到左邊都會有tag，那個就是版本號碼
如這張圖的3.0.0-rc.2.2.1.


因此呢接下來我們直接套用，會變成下面這樣
總之呢，之後帶入會變成這樣。

~~~swift
// swift-tools-version:4.0.0
// The swift-tools-version declares the minimum version of Swift required to build this package.

import PackageDescription

let package = Package(
    name: "test_SPM",
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
        .package(url: "https://github.com/stephencelis/SQLite.swift.git", from: "0.11.4"),
          
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "test_SPM",
            dependencies: ["SQLite"]),
    ]
)


~~~


之後再打開你的command
在你的<project_dir>目錄下
輸入

>swift package update <br>
{:message}
之後會變成如下圖這樣，沒有跳出錯誤訊息，代表成功囉～

之後繼續厦command

>swift package update <br>
>swift build <br>
>swift package generate-xcodeproj <br>
{:message}

之後就能直接使用了

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)



## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

## 參考連結
[Vapor]: https://github.com/vapor/vapor
[Charts]: https://github.com/danielgindi/Charts
[Charts_releases]: https://github.com/danielgindi/Charts/releases
[Enjoy Your Coding 使用 Swift Package Manager 管理套件]: https://sudo.tw/article/xcode-ios-swift-package-manager
