---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: 使用 StarGAN 轉換動漫人物的髮色


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  翻譯 [Lento] 所寫的 [StarGANを用いた髪色変換]。
 
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
翻譯自：[StarGANを用いた髪色変換]
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

本次的目的是將角色的髮色進行轉換(ex. 黒→白)，

雖然使用 [CycleGAN] 可以將動漫角色中的一部分進行轉換，

ex. 將黑頭髮轉成白頭髮，

但是他只能適用於兩個 Domain(這邊可以暫時理解成要學習的特徵空間)，

以髮色為例 [CycleGAN] 只能轉變 黑 → 白 以及 白 → 黑，

意味著如果要轉換成其他顏色的話，

就需要重新訓練一個新的生成器。

對此研究出一個可學習多個不同 Domain 的生成器是必要的，

而 [StarGAN] 的出現恰好解決了這個問題，

只要一個生成器就能生成不同 Domain 的圖像，

在此任務來說就是只要一個模型就能轉換角色為不同髮色，

不再像以往 [CycleGAN] 只能受限於兩個 Domain 的困境。

因此本次會利用 [StarGAN] 來進行動漫角色的髮色轉變。


# [StarGAN]

詳細的說明會省略，

簡單的來說生成器會串接 Domain label 來生成圖像，

StarGAN 與原生的 GAN 不同之處在於，

原生辨別器是學習辨別是真或假（Real / Fake），

而 StarGAN 為了要學習圖像是屬於哪個 Domain label，

因此辨別器多了分類的功能（Domain classification），

基本概念可以參考 [ACGAN]。

![](/assets/img/2021-01-25-StarGAN_hair_color/StarGAN.png)

圖片出自 : Yunjey Choi, et al., “StarGAN : Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation”.

實際上在 StarGAN 的論文中是利用兩個資料集訓練，

同時學習人物特徵以及臉部表情的轉換。

![](/assets/img/2021-01-25-StarGAN_hair_color/StarGAN_arch.png)
圖片出自 : Yunjey Choi, et al., “StarGAN : Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation”.


# 資料集

- [safebooru](https://safebooru.org/) 從該網站收集圖片，

並利用頭髮顏色標籤(Tag)來收集圖像，

例如 Pink_hair 和 Blonde。

最終收集 5 種不同顏色的頭髮
- 白髮
- 藍髮
- 粉髮
- 金髮
- 黑髮

再將其角色的臉切出，

每個顏色各自收集 2,000 張圖片作為資料集，

因此可將此任務視為 5 個不同的 Domain 進行圖像轉換，

分別可將不同圖像的顏色各自轉換(ex. 黒→白, 黒→藍, 黒→粉, 黒→金, 白→黑, 白→藍, 白→粉...)


# 成果

首先我們看看透過這種方式所訓練出來的模型，

是否可以漂亮的轉換髮色，

下方為 StarGAN 訓練 285 個 epoch 的結果。

最左側為輸入圖片，

接著依序為轉白髮, 藍髮, 金髮, 粉髮, 黑髮

![](/assets/img/2021-01-25-StarGAN_hair_color/StarGAN_285epoch.png)


如果是使用 CycleGAN 的話。

會連不相關的背景也都會不小心轉換到該顏色，

不過作者沒有附上相關的圖片。

那下方為加藤惠的髮色轉換，

![](/assets/img/2021-01-25-StarGAN_hair_color/starGAN_girl_Hair.png)

除了白髮的顏色比較微妙之外，

其他顏色都不錯呢，

作者猜測可能是輸入圖像的髮色是棕色而非純黑色，

因為棕髮並不在原本的資料集中可能導致了輸出結果不好。

不過作者表示，不管什麼髮色加藤惠都還是很可愛捏～


# 結論

此次利用了 StarGAN 進行髮色轉換，

只要利用單個模型就可以在 5 個不同 Domains 互相轉換，

不過目前還有一些受限，

像是髮色有很多不同的顏色如：綠髮、棕髮，

還需要透過更多的資料才能轉換更多顏色。

接著我們探討 StarGAN 在測試期間其實可以透過操作條件向量來達到創建新的顏色，

ex. 希望生成紫色的話，

我們可以設定生成一半藍頭髮以及一半紅頭髮的髮色，

就有可能轉換成紫髮。

而作者未來希望不僅是對髮色進行操作，

甚至還可以像 [StarGAN] 原論文一樣對表情進行操作，

作者最終還希望可以對加藤恵的髮型進行操作呢～

最後如果有任何的建議，

請讓我知道。

# 實驗環境

OS : Ubuntu 16.04 LTS (64-bit)

CPU : Intel(R) Core(TM) i5–4590 CPU @ 3.30 GHZ

GPU : NVIDIA GTX970

Memory : 8GB


# 參考資料：

[StarGANを用いた髪色変換]

[Lento]

[StarGAN]

[CycleGAN]

[ACGAN]

[Lento]:https://medium.com/@crosssceneofwindff

[StarGANを用いた髪色変換]:https://medium.com/@crosssceneofwindff/stargan%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E9%AB%AA%E8%89%B2%E5%A4%89%E6%8F%9B-6d35477eab46
[CycleGAN]:https://arxiv.org/pdf/1703.10593.pdf
[StarGAN]: https://arxiv.org/pdf/1711.09020.pdf
[ACGAN]: https://arxiv.org/pdf/1610.09585.pdf