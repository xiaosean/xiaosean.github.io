---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: 透過Python 架設 Line Chatbot 教學-應用篇。

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Line Develop SDK 建置一個簡單的 Line Chatbot - 應用篇

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


# 前置條件

有辦法順利使用Line chatbot達成複誦我傳送過去的訊息

如果沒辦法的話 請參考[透過Python架設Line Chatbot]


# 目標

我們希望要有發送訊息以及回覆訊息的功能，

# 文件區

看一下文件

https://developers.line.me/en/services/messaging-api/ 

https://developers.line.me/en/docs/messaging-api/reference/

https://developers.line.me/en/faq/

# User ID



# 如何獲取User ID

首先我們需要user id才能做推播

這邊的user id並不是只自己在line上面所設定的id

那我們要如何知道user id 呢

文件是這樣寫拉

ID of the target recipient. Use a userId, groupId, or roomId value returned in a webhook event object. Do not use the LINE ID found on the LINE app.

要他回覆才能獲得user id

這部分我是覺得很疑惑拉 連followers的id都拿不到嗎

就先這樣吧。。。 如果有人知道的話再跟我說

那我們先透過回覆獲得User Id吧～

首先我們要知道有人Reply後 我們會獲得什麼東西

於是我們修改reply的程式碼

就可以拿到user id了 這樣寫真智障

反正我們先拿到user id 等等再對他做專門的推播試試

如下圖 我的user id 是 Ufeb-----------------


# 推播

推播部分只要知道user id 就不需要特別架一個server了

因此我用jupyter notebook做示範


在python是使用
line_bot_api.push_message(to, Msg)

Input:
  - to : String
    User Id
  - Msg : Message objects
    傳送的訊息，官網有給幾種物件

    JSON object which contains the contents of the message you send.

    - Text
    - Image
    - Video
    - Audio
    - Location
    - Sticker
    - Imagemap
    - Template

  怎麼使用可以看這
  https://github.com/line/line-bot-sdk-python/blob/master/linebot/models/send_messages.py 

實際操作在jupyter notebook中

那我們在下面只貼出幾個做展示

Text:

~~~python

from linebot import LineBotApi
from linebot.models import TextSendMessage, ImageSendMessage
from linebot.exceptions import LineBotApiError

CHANNEL_ACCESS_TOKEN = "YOUR CHANNEL TOKEN"
to = "YOUR USER ID"

line_bot_api = LineBotApi(CHANNEL_ACCESS_TOKEN)

#文字訊息

try:
    line_bot_api.push_message(to, TextSendMessage(text='台科大電腦研習社'))
except LineBotApiError as e:
    # error handle
    raise e

#圖片訊息
# ImageSendMessage物件中的輸入
# original_content_url 以及 preview_image_url都要寫才不會報錯。
#輸入的網址要是一個圖片，應該說只能是一個圖片，不然不會報錯但是傳過去是灰色不能用的圖
line_bot_api = LineBotApi(CHANNEL_ACCESS_TOKEN)
image_url = "https://i.imgur.com/eTldj2E.png?1"
try:
    line_bot_api.push_message(to, ImageSendMessage(original_content_url=image_url, preview_image_url=image_url))
except LineBotApiError as e:
    # error handle
    raise e
~~~

 ![](/assets/img/22018-04-19-LINE-ChatBot-Usage/Push_test.png)



# 參考連結
Line Message API - https://developers.line.me/en/services/messaging-api/

[方案介紹]: https://at.line.me/tw/plan
[註冊帳號]: https://developers.line.me/en/
[Line Developer]: https://developers.line.me/en/
[Line Message API]: https://developers.line.me/en/services/messaging-api/
[Line Bot SDK Github]: https://github.com/line/line-bot-sdk-python
[透過Python架設Line Chatbot]:http://www.xiaosean.website/server/2018/04/10/LineChatbot/


