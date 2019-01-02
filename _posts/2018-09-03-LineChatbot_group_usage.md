---
# Posts need to have the `post` layout
layout: post
comments: true


# The title of your post
title: 透過Python Line Chatbot 被邀請入群說出謝謝 - Group chats

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  透過Line ChatBot實現進入群組後自動說感謝的功能。
  

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
<!-- image: /assets/img/default.jpg -->

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
Line chatbot 系列 - group chats，

透過Line ChatBot實現被邀請入群組可以自動說謝謝的功能。

# 目標

當機器人被邀請至群組時，

自動的說出感謝的台詞。

![](/assets/img/2018-09-03-LINE-ChatBot-Group-Usage/demo.png)


# 抱怨一下

其實原本是想做標記入群新人的功能，不過看了一下API，似乎是沒有開放標記，

之後又發現其實根本沒有開放有沒有人進入群組的API，

因此就只能達成被邀請入群時說感謝的話，

以及離開時紀錄一下log，

變成挺沒用的一篇文章。

無聊可以看一下下面的帖子：

- 不開放群組的 User Events

https://github.com/line/line-bot-sdk-python/issues/21

# 前置條件

有辦法順利使用Line chatbot達成複誦我傳送過去的訊息。

如果沒辦法的話-請參考[透過Python架設Line Chatbot]

架完後要使用基本的推播/回覆功能-請參考[透過Python使用Line Chatbot-應用篇]。


# 文件區

本人實作的 Line Chatbot 程式碼都會放在 [Github:Xiaosean - Line_chatbot_tutorial]

看一下API文件

https://developers.line.me/en/reference/messaging-api/#join-event

https://pypi.org/project/line-bot-sdk/#source

下方這連結有很多功能的實作（官方的源碼）。

https://github.com/line/line-bot-sdk-python/blob/a3c1947ec00bf78028b57b1e6854a9dca78e6de7/examples/flask-kitchensink/app.py

# 設定

首先我們要使用群組功能的話，

需要到下面頁面做開啟。

https://developers.line.me/console

![](/assets/img/2018-09-03-LINE-ChatBot-Group-Usage/setting0.png)

點擊完你的 Chatbot 後，會到下面這頁面，

之後往下拉， 到 Messaging settings 的位置，

這邊有兩個功能需要啟用:

- Use webhooks 
- Allow bot to join group chats 

![](/assets/img/2018-09-03-LINE-ChatBot-Group-Usage/setting1.png)

![](/assets/img/2018-09-03-LINE-ChatBot-Group-Usage/setting2.png)

都設定好後，可至下面這網址確認。

https://admin-official.line.me/

如果成功的話會如下圖。

![](/assets/img/2018-09-03-LINE-ChatBot-Group-Usage/setting-finish.png)



那我們在下面只貼出簡單的做展示，程式碼使用方式的請至[]

我們主要使用的功能為
- JoinEvent
> 下方為回傳的範例內容
>
> 可以看出我們可以知道我們是加入哪個群組的Id，雖然也沒什麼用就是了。
>
> "replyToken": "nHuyWiB7yP5Zw52FIkcQobQuGDXCTA",
  "type": "join",
  "timestamp": 1462629479859,
  "source": {
    "type": "group",
    "groupId": "Ca56f94637cc4347f90a25382909b24b9"
  }

- LeaveEvent
> 當機器人被踢出群組後，可透過這個Event紀錄住被哪個群組踢出。



~~~python
import json

from flask import Flask, request, abort

from linebot import (
    LineBotApi, WebhookHandler
)
from linebot.exceptions import (
    InvalidSignatureError
)
from linebot.models import (
    MessageEvent, JoinEvent, LeaveEvent, TextMessage, TextSendMessage
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
    print(body)
    # handle webhook body
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        abort(400)

    return 'OK'

@handler.add(JoinEvent)
def handle_join(event):
    newcoming_text = "謝謝邀請我這個機器來至此群組！！我會盡力為大家服務的～"

    line_bot_api.reply_message(
            event.reply_token,
            TextMessage(text=newcoming_text)
        )
    print("JoinEvent =", JoinEvent)

@handler.add(LeaveEvent)
def handle_leave(event):
    print("leave Event =", event)
    print("我被踢掉了QQ 相關資訊", event.source)

                            
@app.route('/')
def homepage():
    return 'Hello, World!'

if __name__ == "__main__":
    app.run()
~~~


# 參考連結

[Line Bot SDK Github]

[Line Message API]

# 若有問題

歡迎在下方留言處留言或是寄e-mail，

若使用 FB 私訊我的話會因為沒加好友，很容易會被評遮忽略。

[Github:Xiaosean - Line_chatbot_tutorial]:https://github.com/xiaosean/Line_chatbot_tutorial
[Line Developer]: https://developers.line.me/en/
[Line Message API]: https://developers.line.me/en/services/messaging-api/
[Line Bot SDK Github]: https://github.com/line/line-bot-sdk-python
[透過Python架設Line Chatbot]:https://xiaosean.github.io/chatbot/2018/04/10/LineChatbot/
[透過Python使用Line Chatbot-應用篇]:http://www.xiaosean.website/chatbot/2018/04/19/LineChatbot_usage/