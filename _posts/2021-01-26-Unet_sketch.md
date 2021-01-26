---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: 使用 Unet 將動漫圖片轉為素描


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  翻譯 [Lento] 所寫的 [Unetを用いた着色画像→線画]。
 
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
categories: [Deep Learning, Computer Vision]
tags: []
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---
翻譯自：[Unetを用いた着色画像→線画]
感謝 [Lento] 授權翻譯，

若對此後續任務有興趣可與作者討論 [Twitter_NieA7_3170](https://twitter.com/NieA7_3170)

由於我挺喜歡動漫的，

所以一直都有關注 [Lento] 的文章，

想說翻譯可以給更多入門的人理解，

順便練習日文，

基於上述種種原因，

所以會開始陸續翻譯，

以及會稍微重構一下原文，

希望能幫助到有需要的人。


# 簡介

若未來要利用 [pix2pix] 對素描上色的首要條件是成對(Pair)的資料集，

然而成對的圖片(A 角色素描以及上色圖片)是難以收集的，

因此在本篇會說明如何將動漫圖片轉為素描，

這樣未來只需上色圖片就可以輕鬆轉換成大量的成對資料集以供 pix2pix 學習。

首先我們可透過 Opencv 簡單的將上色的圖像轉換成素描，

具體來說是將上色圖片的輪廓線給提取出來，

只是利用這種方式會發現提取出來的線條有粗細之分，

但這在素描圖像中是不自然的。

![](/assets/img/2021-01-26-Unet_sketch/opencv_sketch.png)

因此為了生成自然的素描，

這次嘗試使用 [Unet] 來將動漫圖片轉為素描，

原本以為會順利進行，

但後來發現意外的有趣就整理出這篇文章。

# 資料集

- [pixiv](https://www.pixiv.net/) 從該網站收集圖片，

利用手繪(原文：塗ってみた)的標籤(Tag)來收集圖像，

共收集 120 對的上色圖像以及素描。

當下方兩點都符合才會收集該對(上色圖像、素描)圖像

- 圖片中是否有多餘的資訊（ex. 素描明明是要白色的背景，但其背景卻畫了很多種顏色、是否在素描上寫字）

- 上色圖片與素描若有錯位也不挑選

接著將這 120 對圖片隨機擷取 3 塊 128 x 128 的尺寸大小，

接著進行 (0, 90, 180, 270) 度的旋轉作為資料增強，

因此合計有 120 * 3(擷取 3 塊 128 x 128) * 4(旋轉方式) = 1440 對圖片作為資料集。


# 成果

使用上述的資料集訓練 Unet，

輸入為一張上色的圖像，

希望可以輸出素描圖像，

因此在訓練時 Ground Truth 設為素描圖像（灰階），

損失函數使用平均絕對值誤差(Mean absolute error，MAE)。

在實際執行模型時輸出的各個像素點會介於 0 ~ 1 = [0, 1]，

會將輸出值 < 0.9 的設為 0，

其餘為 1（輪廓線）。

下圖為 Size(256 x 256) 的成果
![](/assets/img/2021-01-26-Unet_sketch/Unet-edge.png)

下圖為 Size(512 x 512) 的成果

![](/assets/img/2021-01-26-Unet_sketch/512color.png)

![](/assets/img/2021-01-26-Unet_sketch/512edge.png)

最終利用 Unet 生成的素描就不會像 Opencv 生成的素描有粗細不一的線條了，

雖然有些地方是不連貫的線又或是沒有顯示出輪廓線，

但大致上來說還是不錯的。

# 問題

下方在實驗過程中有遇到兩點問題，

在這做個註記。

- 在一開始收集資料時，有先壓縮圖像的尺寸，因此在縮小時所造成的鋸齒圖片，在輸入進 Unet 後也跑出鋸齒狀的圖片，而此處只要在收集圖片時，改使用裁切(Crop)而非壓縮的方式就能得到改善。

![](/assets/img/2021-01-26-Unet_sketch/resize_zigzag.png)

-  模型結構的選擇，本次是使用 Unet，但在一開始是使用 AutoEncoder，但是輸出的結果不好，因此最後還是使用了 Unet。儘管上色圖片與素描的顏色是不同的，但對於成對且結構相同的圖像，利用 Unet 會有較好的轉換效果，

# 結論

此次利用了 Unet 進行動漫手繪圖像轉換為素描，

大概算是成功的，

但是進行大量圖像轉換的情況還是需要評估的，

因為就目前模型來說儘管利用 GPU 但輸出一張圖像也要運算好幾秒，

然而開頭所說的 Opencv 方式就可以高速的運算並輸出圖片。

未來也希望能嘗試比較 Opencv 的素描以及 Unet 所輸出的素描，

分別應用至 pix2pix 的素描上色任務會有什麼不同的結果。


> 譯者補充(2021.Jan.26)：以近期的中階 GPU（Nvidia 2080 up）運算應該是不需要一秒的。

# 實驗環境

OS : Ubuntu 16.04 LTS (64-bit)

CPU : Intel(R) Core(TM) i5–4590 CPU @ 3.30 GHZ

GPU : NVIDIA GTX970

Memory : 8GB


# 參考資料：

[Unetを用いた着色画像→線画]

[Unet]

[pix2pix]

[Lento]

[Lento]:https://medium.com/@crosssceneofwindff

[Unetを用いた着色画像→線画]:https://medium.com/@crosssceneofwindff/unet%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E7%9D%80%E8%89%B2%E7%94%BB%E5%83%8F-%E7%B7%9A%E7%94%BB-1cff6652941c

[Unet]:https://arxiv.org/pdf/1505.04597.pdf
[pix2pix]:https://arxiv.org/pdf/1611.07004.pdf
