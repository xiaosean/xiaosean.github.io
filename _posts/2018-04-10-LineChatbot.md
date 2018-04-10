---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Line Chatbot 教學及簡單使用Message API 發送及回覆。

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Line Develop SDK 建置一個簡單的 Line Chatbot

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


# 方案選擇

我們可以點進這頁面看看詳細的資訊[方案介紹]

那我們這邊可以看到

![](/assets/img/2018-04-10-LINE-ChatBot/plan-1.png)



![](/assets/img/2018-04-10-LINE-ChatBot/plan-2.png)

我要教社課一定會選擇免費的！！

那因為我們需要有發送訊息(Push API)以及回覆訊息(Reply API)的功能

因此我們就是選擇**Developer Trial**

# 註冊帳號

我們要進入[Line Developer]

然後點選 **Start Using Message API**

之後輸入你的帳號密碼

那接下來會進到這頁面

![](/assets/img/2018-04-10-LINE-ChatBot/register-page.png)

填一填之後，到下個頁面有個Start的按鈕按下去～

因為目前沒有provider

因此下面這邊我們點選左邊的Add new provider

![](/assets/img/2018-04-10-LINE-ChatBot/Create-New-Develop.png)

然後點選 **Message API**

![](/assets/img/2018-04-10-LINE-ChatBot/Click-Message-API.png)

然後把你的資訊填進去

比較值得注意的是 下面的選項記得選Develop trial(預設就是這個)

![](/assets/img/2018-04-10-LINE-ChatBot/info-1.png)

![](/assets/img/2018-04-10-LINE-ChatBot/info-3.png)

之後會出現下圖

![](/assets/img/2018-04-10-LINE-ChatBot/Finish-Provider.png)

點擊你剛剛創建出來的Provider

然後會進入到這個頁面

![](/assets/img/2018-04-10-LINE-ChatBot/Chatbot-info-mask.png)


這個頁面有幾個個重要的資訊是

- Channel ID

- Channel secret

- QR Code
  
  先拿起你的手機掃描條碼成為好友，之後才能夠順利的進行測試。

- Channel access token(long lived)

  這個我覺得應該有他的功用

  不過我目前只看到有人用這個token和EasyChat做整合

  不清楚呼叫api有沒有影響


>Channel ID 以及 Channel secret 這兩組號碼要保存好，之後要填入到API中。
{:.message}



# 實際應用

在一切開始之前

我看了看文件

>The Messaging API allows for data to be passed between the server of your bot application and the LINE Platform. When a user sends your bot a message, a webhook is triggered and the LINE Platform sends a request to your webhook URL. Your server then sends a request to the LINE Platform to respond to the user. Requests are sent over HTTPS in JSON format.
{:.message}

重點應該是這兩句拉
- LINE Platform sends a request to your webhook URL
- Requests are sent over HTTPS in JSON format.

你要給一個URL => webhook的服務

你的網址要是https

原來要server呢（（廢話


然後再看下面這個Link  

https://developers.line.me/en/docs/messaging-api/building-bot/

那官方是推薦使用 [Heroku]

咳。。。 我先來去寫另一篇文章好了 Flask + Heroku




接下來我們點擊進[Line Bot SDK Github]

看看文件怎麼寫

首先我們照著文件安裝

>pip install line-bot-sdk
{:.message}

將github上的**Synopsis**部分
複製貼上到一個.py檔案




~~~python
from flask import Flask, request, abort

from linebot import (
    LineBotApi, WebhookHandler
)
from linebot.exceptions import (
    InvalidSignatureError
)
from linebot.models import (
    MessageEvent, TextMessage, TextSendMessage,
)

app = Flask(__name__)

line_bot_api = LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')
handler = WebhookHandler('YOUR_CHANNEL_SECRET')


@app.route("/callback", methods=['POST'])
def callback():
    # get X-Line-Signature header value
    signature = request.headers['X-Line-Signature']

    # get request body as text
    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)

    # handle webhook body
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        abort(400)

    return 'OK'


@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=event.message.text))


if __name__ == "__main__":
    app.run()
~~~

之後將 15, 16行的下方兩個參數，替換為自己的CHANNEL TOKEN
>line_bot_api = LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')
>handler = WebhookHandler('YOUR_CHANNEL_SECRET')
{:.message}


===========未完待續，我先去架個flask + Heroku



# 參考連結
Line Message API - https://developers.line.me/en/services/messaging-api/

[方案介紹]: https://at.line.me/tw/plan
[註冊帳號]: https://developers.line.me/en/
[Line Developer]: https://developers.line.me/en/
[Line Message API]: https://developers.line.me/en/services/messaging-api/
[Line Bot SDK Github]: https://github.com/line/line-bot-sdk-python
[Heroku]: https://www.heroku.com/