---
# Posts need to have the `post` layout
layout: post
comments: true


# The title of your post
title: 透過Python Line Chatbot 建立聊天機器人 - 應用篇。

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用Line Develop SDK 的 Python Line bot sdk 建置一個簡單的 Line Chatbot，教導reply、push、message等等的相關教學
  

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


# Line Chatbot
  可以透過不寫程式來達成一些功能

  https://admin-official.line.me/

    - 預約傳送訊息
    - 加入好友的歡迎訊息

      這部分也可透過寫程式時做follow event.      

    - 圖文選單

      這功能相當好用，
      可以再視窗下方建立好幾顆功能，
      可透過點擊後回傳文字，結合Reply使用。


  那下面主要是介紹寫程式可完成的功能。


# User ID - 如何獲取 （（如果不想特定推播給單一使用者可略過

首先我們需要user id才能做推播

這邊的user id並不是只自己在line上面所設定的id

那我們要如何知道user id 呢

文件是這樣寫拉

## 取得本人的 user id 可直接從網頁得知

![](/assets/img/2018-04-19-LINE-ChatBot-Usage/console-user-id.png)

## 另一個方式可以透過伺服器取得

ID of the target recipient. Use a userId, groupId, or roomId value returned in a webhook event object. Do not use the LINE ID found on the LINE app.

要他回覆才能獲得user id

這部分我是覺得很疑惑拉 連followers的id都拿不到嗎

就先這樣吧。。。 如果有人知道的話再跟我說

那我們先透過回覆獲得User Id吧～

首先我們要知道有人Reply後 我們會獲得什麼東西

於是我們修改reply的程式碼

如下列網址：

https://github.com/xiaosean/Line_chatbot_tutorial/blob/master/show_how_to_get_user_id.py

主要的程式碼如下：
~~~python

@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    # get user id when reply
    user_id = event.source.user_id
    print("user_id =", user_id)
~~~

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
    - Sticker(貼圖)
    - Imagemap
    - Template
      - button - template
      - Carousel - template
      - Image Carousel - template
  Message objects 怎麼使用可以看這
  https://github.com/line/line-bot-sdk-python/blob/master/linebot/models/send_messages.py 

實際操作在[Push message ipynb tutorial]中

比較常用的應該是template的呈現，如下圖

Carousel-button

 ![](/assets/img/2018-04-19-LINE-ChatBot-Usage/Carousel-button.png)




那我們在下面只貼出簡單的做展示，程式碼使用方式的請至[Push message ipynb tutorial]

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

 下圖為上述之程式碼展示。

 ![](/assets/img/2018-04-19-LINE-ChatBot-Usage/Push_test.png)

# Temple Action - Template樣板中能達到的功能
  - MessageTemplateAction

    label參數 - 顯示在選項中的文字
    text參數 - 點擊該選項後，會發送出什麼樣的文字訊息

  - PostbackTemplateAction

    特色是除了text/label的參數欄，還有data的參數欄<br>
    可將text設定為None，達成用戶不用回傳文字，但是卻可以回傳資料的功能。<br>
  
  - URITemplateAction

    特色是除了text/label的參數欄，還有uri的參數欄<br>
    點選到該按鈕會連結至某個網址。

  - DatetimePickerTemplateAction

    這功能比較新，點選後會出現picker可以選擇日期/時間。

  - ImagemapAction

    下面兩個主要是搭判Imagemap的template使用，<br>
    特點為有了Area的參數，可設定一個區塊。
    
    - MessageImagemapAction
      可設定點選到圖片哪個區塊會回傳什麼樣的文字。

    - URIImagemapAction
      可設定點選到圖片哪個區塊會連結至某個網址。

實際操作在[Push message ipynb tutorial]中

# Reply
  
  處理回覆訊息有幾種方式/事件
  - MessageEvent

    只要對方發送訊息，<br>
    就會進到這個function去做處理，<br>
    對於初學者來說，<br>
    大多的處理都可以在這完成，<br>
    透過if-else去做就可以完成很多功能。<br>

  - PostbackEvent  

    這邊是專門處理經由PostbackTemplateAction所發送出來的請求<br>
    [Reply example code]

  - FollowEvent

    當追蹤的時候會觸發
    [follow event example]

  - UnfollowEvent
    
    當取消追蹤的時候會觸發

  - JoinEvent
  - LeaveEvent
  - BeaconEvent
    這功能建議看這篇[LINE Developer Day 見聞 — 火紅的 BOT API]



實際操作在[Reply example code]中

# Rich menu

https://developers.line.me/en/docs/messaging-api/using-rich-menus/

Two ways of creating rich menus

You can create rich menus for your bot with the Messaging API or the LINE@ Manager (content management tool for LINE@ accounts). Both methods can be used at the same time.

Rich menus that are linked to a specific user via the Messaging API will override the rich menu set in the LINE@ Manager.

有兩種方式可以完成
  - 在網頁上設定好，我認為這已經做得很方便了。

    https://admin-official.line.me/

  - 使用API設定
    
    我實際使用API時，連RichMenu都不能import<br>
    奇怪 他這unittest怎麼會過<br>
    之後等勇者嘗試<br>
    https://github.com/line/line-bot-sdk-python/blob/master/tests/api/test_rich_menu.py


# 參考連結
Line Message API - https://developers.line.me/en/services/messaging-api/

[LINE Developer Day 見聞 — 火紅的 BOT API]

[Day15 Line ChatBot Messaging types下集]

[方案介紹]: https://at.line.me/tw/plan
[註冊帳號]: https://developers.line.me/en/
[Line Developer]: https://developers.line.me/en/
[Line Message API]: https://developers.line.me/en/services/messaging-api/
[Line Bot SDK Github]: https://github.com/line/line-bot-sdk-python
[透過Python架設Line Chatbot]:https://xiaosean.github.io/chatbot/2018/04/10/LineChatbot/
[Push message ipynb tutorial]:https://github.com/xiaosean/Line_chatbot_tutorial/blob/master/push_tutorial.ipynb
[Reply example code]:https://github.com/xiaosean/Line_chatbot_tutorial/blob/master/line_chatbot_reply.py
[follow event example]:https://github.com/xiaosean/Line_chatbot_tutorial/blob/master/line_chatbot_follow.py
[LINE Developer Day 見聞 — 火紅的 BOT API]:https://medium.com/@yurenju/line-developer-day-%E8%A6%8B%E8%81%9E-%E7%81%AB%E7%B4%85%E7%9A%84-bot-api-bfbf1b97b0b4
[Day15 Line ChatBot Messaging types下集]:https://ithelp.ithome.com.tw/articles/10195640?sc=iThomeR

