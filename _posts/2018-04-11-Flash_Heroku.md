---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Python Flask 結合 Heroku 架一個Https的伺服器(未完成)

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Python的Flask結合Heroku建置一個Https伺服器

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
categories: [Chatbot]
tags: [example, Line]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---
透過Line ChatBot實現一些推播以及回覆的功能

前言：主要是因為要教台科大電腦研習社的社課，想了想我覺得有趣又會想要學的應用，
於是就想到**Line ChatBot**了

目的：希望最終可以有Taxi go的那種圖形介面。因此我們要有發送訊息以及回覆訊息的功能。

那我們看一下文件

https://developers.line.me/en/services/messaging-api/ 

我們希望要有發送訊息以及回覆訊息的功能，

因此我們要選擇使用 **Message API**
![](/assets/img/2018-04-10-LINE-ChatBot/push_and_reply.png)
上圖轉載自[Line Message API]



# 註冊帳號

首先我們先進入[Heroku]，

然後點選 **Sign Up For Free**

之後註冊一些資訊

主要是language要選擇 ** Python **

![](/assets/img/2018-04-11-Flask-Heroku/Register_Info.png)


那接下來去信箱收驗證信

然後會跳轉到設定密碼

Minimum 8 characters: Letters, numbers, and/or symbols

這密碼也太麻煩。。。

![](/assets/img/2018-04-11-Flask-Heroku/app_homepage.png)

點擊**create new app**

之後App name隨便打

然後我開始參考下面官方文檔連結來做安裝
https://devcenter.heroku.com/articles/getting-started-with-python#introduction


![](/assets/img/2018-04-11-Flask-Heroku/Install-instruction.png)

-- a free Heroku account.

  我們剛剛已經創建好了
-- Python version 3.6 installed locally - see the installation guides for OS X, Windows, and Linux.
  到下面的網頁
  https://devcenter.heroku.com/articles/getting-started-with-python#set-up

  點擊此Download the Heroku CLI下載

  選擇你的作業系統進行安裝

  >如果沒有git的話，記得在安裝的時候點擊git的選項安裝
  {.leading}

  ![](/assets/img/2018-04-11-Flask-Heroku/Download-the-Heroku-CLI.png)

  之後我們就可以使用command呼叫heroku的指令

-- Pipenv installed locally. Accomplish this by running pip install pipenv.
  
  按照官方文檔是建議使用他們已經有的example

  我們再加以更改會比較簡單

  實際做法是用command呼叫這2行


  >git clone https://github.com/heroku/python-getting-started.git
  .{message}

  >cd python-getting-started
  .{message}

# 參考連結
[Deploying-Flask-To-Heroku]


[Deploying-Flask-To-Heroku]: https://github.com/twtrubiks/Deploying-Flask-To-Heroku
[Heroku]: https://www.heroku.com/

<!--Heroku=> xi-s-000 -->