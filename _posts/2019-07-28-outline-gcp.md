---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: 在 GCP(Google Cloud Platform) 上面架設 Outline VPN

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: > 
  簡單來說呢，Outline vpn 是一個可以快速設定好 VPN Server 的工具，支援多個平台，可以快速的設定，以我個人的 Server 架設在 GCP，然後是使用 Windows 的作業系統做管理， 用 Mac 以及 iphone 來連線。

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
<!-- image: /assets/img/default.jpg -->
<!-- hide_image: true -->

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [SERVER]
tags: []
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---
當初出差的時候，因為一些需求，要用到台灣的網路。

有幾個方法可以選擇：

- 在台灣有電腦開著，用 Anydesk 或是 Teamviewer 連線到台灣的電腦，透過這種方式上網
- 使用漫遊或是某電信的 Sim 卡上網
- 使用免費 VPN
- 架設自用 VPN

而我選擇自己架設 VPN 有幾個原因
- 單純想架學點新的東西。
- 無法確保免費 VPN 的網路連線品質。
- 不知道免費 VPN 在背後有沒有做什麼奇怪的事。

在這邊我們選用 Outline VPN，

主要是因為它設定簡單又可支援多個不同的平台。

連線很簡單拉，設定其實沒有很簡單。。。

在[Outline]官網中提及:記者需要透過安全的管道存取資訊，以針對議題進行研究、與消息來源聯絡及報導新聞。Outline 可讓新聞機構輕鬆為自家網路提供更安全的開放網路存取管道。

詳情請看
- Outline: https://getoutline.org/zh-TW/home
- Wiki: https://zh.wikipedia.org/wiki/Outline_VPN

這篇文章我們會使用 GCP - google cloud platform 當作 VPN 的 Server，

因此有幾個前置條件：
- Google 帳號
- 一張信用卡（因為 Google 有贈送首年 300 美金的免費額度）

對這專案有興趣的可以去看看
Github: https://github.com/Jigsaw-Code/outline-client

# Outline VPN

首先我們點進官網

https://getoutline.org/zh-TW/home

Outline 有兩大部分
- Manager 
- Client

這兩個都支援多種平台，

我會以 Windows 10 去做示範如何架設好 Outline Manager

# Outline Manager

![](/assets/img/2019-07-28-outline-gcp/outline-server.png)

首先下載完之後點開，

![](/assets/img/2019-07-28-outline-gcp/outline-setup.png)

本文採用 Google Cloud Platform，

所以我們可以點擊右上方 GCP，

點擊之後它上面會有介紹怎麼做，

那這篇文章會一步一步地帶你做，

可能未來他的流程會改變，那這篇文章可能就會不適用。。。

![](/assets/img/2019-07-28-outline-gcp/outline-manager-config.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-outline.png)

首先要點 [Add a new firewall rule]: 

https://console.cloud.google.com/networking/firewalls/add

那這時候會到 GCP 的畫面，

我們要先登入 Google 帳號，

並且綁定信用卡拿首年 300 美金的優惠！！

看到右上方，有個藍色的啟用按鈕，點進去設定。

![](/assets/img/2019-07-28-outline-gcp/free-300.png)

啟用之後我們就可以開始設定 GCP 相關的東西～

有 Google 給的 300 美金，

基本上一個人用的 VPN 應該是夠用了。

## GCP 設定

可以再點一次這個連結 要點 [Add a new firewall rule]: 

https://console.cloud.google.com/networking/firewalls/add

創建 GCP 的專案

![](/assets/img/2019-07-28-outline-gcp/gcp-new-project.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-new-project-detail.png)

創立完之後要確保我們有切換到目前的專案！！

接著可以再點一次這個連結 要點 [Add a new firewall rule]:

https://console.cloud.google.com/networking/firewalls/add

開始設立防火牆的規則！

![](/assets/img/2019-07-28-outline-gcp/add-firewall.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-add-firewall.png)

![](/assets/img/2019-07-28-outline-gcp/firewall-rules.png)

設立完規則後，

我們要創立一個機器 - VM Instance，

![](/assets/img/2019-07-28-outline-gcp/gcp-create-vm.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-engine-welcome.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-create-engine.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-create-finish.png)

建立完機器之後，

我們要將機器配置剛剛我們設定的防火牆規則。

![](/assets/img/2019-07-28-outline-gcp/gcp-vm-edit.png)

![](/assets/img/2019-07-28-outline-gcp/gcp-add-outline-tag.png)

然後按下儲存，

按照官方的說明這時候會進到2, 3兩步，

但其實中間官方的說明少掉了好幾步阿！！！！！

對於不會用 GCP 的人一定滿頭問號？？？？

照理官網的步驟，下一步是要貼下面這段，

![](/assets/img/2019-07-28-outline-gcp/step1to2.png)

Note: 下方這段可能會更新，請依照官方文件為主！
~~~bash
sudo bash -c "$(wget -qO- https://raw.githubusercontent.com/Jigsaw-Code/outline-server/master/src/server_manager/install_scripts/install_server.sh)"
~~~

但應該在貼在哪勒?

首先我們要先連線到剛剛創建好的 VM Instance 的機器，

![](/assets/img/2019-07-28-outline-gcp/start-machine.png)

![](/assets/img/2019-07-28-outline-gcp/ssh-init.png)

![](/assets/img/2019-07-28-outline-gcp/ssh-input-bash.png)

這串很重要 複製起來
按照官方教學
有了這串之後

{"apiUrl":"https://xxxxxxxxxxx","certSha256":"xxxxxxxxxxx"}

就可以輸入上方的那串到這裡

![](/assets/img/2019-07-28-outline-gcp/paste-failed.png)

等等，竟然成功了？？？

這和我一個月前嘗試的不一樣

![](/assets/img/2019-07-28-outline-gcp/outline-success.png)

摁。。。 如果失敗的話，

拉到最後面我有補充那我上次失敗的時候做了什麼事。

接著我們要拿這個 Server 的 ss key !!

![](/assets/img/2019-07-28-outline-gcp/outline-shareing.png)

![](/assets/img/2019-07-28-outline-gcp/outline-ss.png)

透過上面的操作會拿到下面這組數字，

這組務必保留好！！

建議 Mail 給自己留個紀錄 ，

因為我 mac 重開機要再次輸入這個 ss 序號才能再次連線(我的 mac 有這困擾, iphone 倒是沒差)，

拿到 ss://xxxxxxxxxxxxxxx，

下一步就是我們要使用 Outline client連線到這個 VPN。

# Outline client

![](/assets/img/2019-07-28-outline-gcp/outline-client.png)

看你要在哪個裝置上使用 我是用過 mac 和 iphone 都很簡單！

只要安裝完 輸入 ss://xxxxxxxxxxxxxxx 就能自動配對連線了，

iPhone 的示範：

![](/assets/img/2019-07-28-outline-gcp/iphone-connect.jpg)

![](/assets/img/2019-07-28-outline-gcp/iphone-connect-success.jpg)


mac 的示範：

![](/assets/img/2019-07-28-outline-gcp/mac-connect.png)

# 失敗集

剛剛那畫面的下面有這兩行：

Make sure to open the following ports on your firewall, router or cloud provider:
- Management port 38347, for TCP
- Access key port 55087, for TCP and UDP

我們要把對應的 port 開起來。

我在那台機器打上：
~~~bash
sudo apt install ufw
sudo ufw enable
sudo ufw allow 38347/tcp
sudo ufw allow 55087/tcp
sudo ufw allow 55087/udp
~~~

# 參考資料：

[Outline]

[Outline]:https://getoutline.org/zh-TW/home

[Add a new firewall rule]:https://console.cloud.google.com/networking/firewalls/add



