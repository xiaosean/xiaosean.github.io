---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: VSCode live share 使用入門，從安裝到多人協作寫code

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  使用 VSCode 最近所推出的套件 live share 套件來達到即時多人協作寫code.
  

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
categories: [VSCODE]
tags: [example]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---
使用 VSCode 最近所推出的套件 live share 套件來達到即時多人協作寫code.

使用起來像是使用 google 文件一樣，簡單直覺！

也能夠很清楚的明白對方目前在修改哪個部分。

先看結果吧！！

![](/assets/img/2018-05-19-VSCode-Live-share/result.png)

還有一個好處是不用因為安裝環境的問題而擔心，

舉例來說A在做深度學習，但是遇到問題了向我求救，

可是他在那台電腦使用的是pytorch + cuda，

那我手邊就一台mac，如果要幫他修程式的話，

我就必須下載那堆東西，才有辦法能跑他的程式碼，

聽起來就很麻煩

這時候就是 live share 登場的時候！！

幫人Debug神器！！

根本工具人再進化呢，

完蛋，以後連見面一起寫code的機會都要沒了...



# 前置條件

下載VSCode

https://code.visualstudio.com/


# 目標

使用 VSCode 的 live share 套件來達到即時多人協作寫code.

# 安裝

打開VS Code後， 點擊左側欄最下面的那個選項

之後的介面會是這樣

 ![](/assets/img/2018-05-19-VSCode-Live-share/search_live_share.jpg)

輸入live share

點擊安裝，安裝完後重啟VS Code

之後會發現介面長這樣，

下方多了一個Sign in的按鈕

 ![](/assets/img/2018-05-19-VSCode-Live-share/under_sign_in.jpg)


# 註冊

點擊下方 Sing in 的按鈕，

 ![](/assets/img/2018-05-19-VSCode-Live-share/under_sign_in.jpg)

之後瀏覽器會開啟一個頁面，

選擇是要使用Microsoft的帳號或是Github的帳號登入，

我是使用Github的，

反正之後就一直勾選同意之類的就好了，

完成後就可以把頁面關掉了。（（這邊忘記截圖...

![](/assets/img/2018-05-19-VSCode-Live-share/ready_to_collaborate.png)

此時你就會發現，

VSCode IDE下方出現的字樣從Sign in 變為 username Share 了

![](/assets/img/2018-05-19-VSCode-Live-share/under_share.jpg)


# 使用方式

點擊下方的Share，

![](/assets/img/2018-05-19-VSCode-Live-share/under_share.jpg)

之後他會自動把網址加入你的剪貼簿，

![](/assets/img/2018-05-19-VSCode-Live-share/click_share.png)

這時候只要將網址貼入瀏覽器，

就會出現下圖，這時候只要點擊click

![](/assets/img/2018-05-19-VSCode-Live-share/chrome_open.png)

之後就會打開VSCode，

這時候已經連進別人的VSCode囉～

你就可以開心的多人協同打code!

完成圖！！

![](/assets/img/2018-05-19-VSCode-Live-share/result.png)

# 進階

當別人要連線時，需要詢問才能連線

![](https://docs.microsoft.com/zh-tw/visualstudio/liveshare/media/vscode-join-approval.png)


相關細節或是進階使用可參考文檔～

https://docs.microsoft.com/zh-tw/visualstudio/liveshare/reference/security 



# 小結

VS Code這次出這個live share應用的確很吸引人，

當需要多人同時寫code時，或許是可以考慮的，

不過這個只要開的人關掉 VS Code其他人也會中斷連線呢，

也是有一點點麻煩的地方。

不過在單人寫code時，

還是比較習慣使用pycharm（（因為我個人主要都寫python，

如果VSCode還有推出什麼很好的應用是PyCharm所沒有的，

歡迎在下方留言推薦喔～
