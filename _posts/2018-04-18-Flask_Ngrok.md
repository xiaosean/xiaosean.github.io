---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Python Flask 結合 Ngrok 架一個本地端的Https伺服器

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Python的Flask結合Ngrok建置一個Https伺服器

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
categories: [SERVER]
tags: [example, Line]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---
透過Ngrok結合Flask，
來架設一個簡單的伺服器，
與下一篇的Line ChatBot做結合
有興趣的可以參考 - [Python使用Line Chatbot]



# 介紹

Ngrok 提供的是一個Https網址可以連結到本地端架設server

為什麼要這樣做呢？

因為通常Webhook都要求https的網址

以前我們通常都怎麼做呢

都是將code 部署到一台伺服器上再做測試

這樣子其實改一下 部署 有問題再改一下 再部署

每次部署少說1分鐘

一天這樣修改下來 來來回回也會浪費不少時間

所以呢 Ngrok當初所提出的概念就是

Spend more time programming. One command for an instant, secure URL to your localhost server through any NAT or firewall.


----------

至於付費方案嗎

我們使用免費版就好

For quick demos and other simple tunneling needs.

HTTP/TCP tunnels on random URLs/ports

限制如下

- 1 online ngrok process

- 4 tunnels/ngrok process

- 40 connections / minute


# 註冊

首先我們先進入[Ngrok]，

然後點選 **Sign Up For Free**

之後註冊一些資訊

註冊完成後

他會顯示一個頁面

照著做就可以正常運作了

值得一提的是有個Auth需要記起來

等我們安裝完應用程式後，會需要用到這行！！

> ./ngrok authtoken 5aN------------------------



![](/assets/img/2018-04-18-Ngrox-flask/auth_mask.png)



# Download

 進入[Ngrok]，點選Download

 那就照著上面的步驟做，應該不難

 到剛剛下載的目錄，解壓縮

 >unzip /path/to/ngrok.zip
 
 用command到剛剛解壓縮完的目錄

 將剛剛叫你們記起來的Auth貼上來

 >./ngrok authtoken -YOUR_AUTH_TOKEN-

 之後會說Authtoken saved to configuration file: XXXXXX



 那接下來輸入下面這行 


 >./ngrok http 80

 之後出現這個畫面

  ![](/assets/img/2018-04-18-Ngrox-flask/port80.png)


 他有給你網址

 這樣就成功了

# 結合flask

因為flask預設的port是5000

所以我們開一個port5000的來用用

 >./ngrok http 5000

 ![](/assets/img/2018-04-18-Ngrox-flask/port5000.png)


先寫一個.py的程式碼
叫做flask_offline.py好了

~~~python

from flask import Flask, request, abort

app = Flask(__name__)

@app.route('/')
def homepage():
    return 'Hello, World!'

@app.route('/test')
def in_test_page():
    return 'In test page'

if __name__ == "__main__":
    app.run()
~~~


之後開啟這個flask

python flask_offline.py 

至瀏覽器輸入在ngrok上給的網址

成果如下圖

![](/assets/img/2018-04-18-Ngrox-flask/result.png)

啊 這樣就結束了

好像有點簡單。。。


# 參考連結
[Ngrok]

[Ngrok]: https://ngrok.com/
[Python使用Line Chatbot]:http://www.xiaosean.website/server/2018/04/10/LineChatbot/


<!--ngrok=> xi-s-000 -->