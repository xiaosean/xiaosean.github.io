---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: 透過Python Line Chatbot 建立聊天機器人 - 基礎篇。

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Line Develop SDK 的Python Line bot sdk 建置一個簡單的 Line Chatbot的環境教學。

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
於是就想到**Line ChatBot**
此部分只教到可以成功架起來，使用方式會寫在下一篇。

那我們看一下文件

https://developers.line.me/en/services/messaging-api/ 

我們希望要有發送訊息以及回覆訊息的功能，

因此我們要選擇使用 **Message API**
![](/assets/img/2018-04-10-LINE-ChatBot/push_and_reply.png)
上圖轉載自[Line Message API]

# 前置條件

需要有一個https的Server

我在這提供幾個簡單的選項，那下面這兩個我都有寫過教學文章

Heroku
  好處：
  - 有https的網址
  - 自己的主機可以不用開著
  - 可以將code丟上去，在開著他的伺服器跑你的程式碼

  教學文章：[Flask結合Heroku教學]


Ngrok
  好處：
  - 有https的網址
  - 可以直接從網址連結至本地端的主機
  - 部署簡單
  - 容易測試，不用每次修改都push上遠端主機
  教學文章：[Flask結合Ngrok教學]


# 流程說明

1.至line申請develop帳號
2.結合[Line Bot SDK Github]
3.可用Line Chatbot複誦我傳過去的訊息

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

  這個token是之後串接Message API會需要的


>Channel ID 以及 Channel access token 這兩組號碼要保存好，之後要填入到API中。
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

接下來我們點擊進[Line Bot SDK Github]

看看文件怎麼寫

首先我們照著文件安裝


有兩種選擇
  
  1.你已經有server 或是架設好 ngrok

    >pip install line-bot-sdk
    {:.message}

  2.如果是使用Heroku架server的方式

    要用pipenv

    >pipenv install line-bot-sdk

    >pipenv shell


將github上的**Synopsis**部分
複製貼上到一個.py檔案


假設檔名為 flask_test.py

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
>
>handler = WebhookHandler('YOUR_CHANNEL_SECRET')
{:.message}

這兩個TOKEN是在下圖的這兩個位置

![](/assets/img/2018-04-10-LINE-ChatBot/all_token_mask.png)

之後架起server

python flask_test.py

然後要了解目前的https的網址


之後去line chatbot設定我們的server的網址

以heroku示範

這次我的heroku的名稱為

mysterious-stream-19548

所以在line的這個頁面要這樣設定

記得網址後面要加/callback如下圖

![](/assets/img/2018-04-10-LINE-ChatBot/callback_link.png)




接下來會用此示範

至於 Webhook URL Requires SSL 的Verify 其實可以不用管他

https://github.com/line/line-bot-sdk-python/issues/37

只要可以像下面這樣正常回答你說過的話就好


<img src="/assets/img/2018-04-10-LINE-ChatBot/Line_ChatBot_Reply.png" style="width:80%; border-radius:10px; padding:5px 0 5px 0;">



其他的使用方法會寫在[Line chatbot 應用篇]







# 參考連結
Line Message API - https://developers.line.me/en/services/messaging-api/

[方案介紹]: https://at.line.me/tw/plan
[註冊帳號]: https://developers.line.me/en/
[Line Developer]: https://developers.line.me/en/
[Line Message API]: https://developers.line.me/en/services/messaging-api/
[Line Bot SDK Github]: https://github.com/line/line-bot-sdk-python
[Heroku]: https://www.heroku.com/
[Flask結合Heroku教學]:http://www.xiaosean.website/server/2018/04/11/Flash_Heroku/
[Flask結合Ngrok教學]:http://www.xiaosean.website/server/2018/04/18/Flask_Ngrok/
[Line chatbot 應用篇]:http://www.xiaosean.website/chatbot/2018/04/19/LineChatbot_usage/
