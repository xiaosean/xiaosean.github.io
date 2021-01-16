---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: 用於 GAN 生成之動漫圖片資料集


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  翻譯 [Lento] 所寫的 [GANを用いたイラスト生成のデータセット]。
 
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
翻譯自：[GANを用いたイラスト生成のデータセット]
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

現今越來越多生成式對抗網路 Generative Adversarial Networks（GAN）用於動漫圖片生成，

由於個人對這領域有興趣，

因此本文介紹用於 GAN 圖像生成模型的動漫資料集，

圖像生成任務的品質會取決於資料集的圖片品質，

本文會利用兩種圖像生成模型來評估各個資料集所生成的圖像品質，
- DCGAN
- Spectral Normalization GAN(SN-GAN)

# 資料集

本次準備兩種資料集

- [safebooru](https://safebooru.org/)，從該網站收集圖片，利用 opencv + lbpcascade_animeface.xml 將動漫圖像取出 64 x 64 大小的臉部圖片共 50,000 張以供模型訓練，下方為 25 張圖片的示意圖。

![](/assets/img/2021-01-16-GAN_illustration_dataset/safebooru.png)

- [Getchu](http://www.getchu.com/)資料集，參考 [makegirlsmoe](https://arxiv.org/pdf/1708.05509.pdf) 的論文，從 [Getchu](http://www.getchu.com/) 網站中收集 25,000 張角色的臉的圖片。與 safebooru 資料集不同的是該類的圖像多半是從遊戲中(註1)取得的，因此背景多半為透明/白的，以及頭像都是屬於正臉的。

註1：Getchu 網站中的遊戲人物圖像指的是「立ち絵(TACHIE)」，一般遊戲中先製作的角色，沒有背景，用於戀愛劇情/文字類遊戲等等的用途。

![](/assets/img/2021-01-16-GAN_illustration_dataset/Getchu.png)

準備兩個不同的資料集的用意是為了評估
- 簡單的收集網路上動漫角色的臉的資料集是否可行
- 背景的顏色對模型訓練是否有影響
- 各資料集整張臉生成的品質



# GAN 模型

如開頭所提到的，本次將上述的 safebooru 以及 Getchu 資料集評估在下方兩個模型

- [DRAGAN]
[DRAGAN] 指出以往 GAN 在訓練辨別器(Discriminator)時，辨別器學習某些真實樣本時，會造成較大的梯度(Gradient)，而這將使得模型訓練變得不穩定，甚至造成模式崩潰(Mode collapse)，意指生成出的圖片已缺少變化，都生成同一種類的圖片。對此提出透過 Noise 的方式平穩訓練流程，其可視為利用 Lipschitz 限制辨別器學習時斜率不要過大。
此方法是基於 DCGAN 的延伸。

- [SNGAN]
[SNGAN] 提出 Spectral Normalization 來作為 Lipschitz 的限制，

並且在 Discriminator 的每層中加入 Spectral Normalization 達到更好的生成效果，

透過這種方式可取代以往的 Batch Normalization，可讓參數量更近一步地減少。

程式碼是參考此處 [pfnet-research chainer-gan-lib](https://github.com/pfnet-research/chainer-gan-lib)

# 成果

首先是 DRAGAN 的生成結果，

### DRAGAN 訓練於 safebooru 資料集
![](/assets/img/2021-01-16-GAN_illustration_dataset/DRAGAN.png)

### DRAGAN 訓練於 Getchu 資料集
![](/assets/img/2021-01-16-GAN_illustration_dataset/DRAGAN-Getchu.png)

由於計算上的問題此次的訓練只有少少的 350 個 Epoch，

盡請見諒。

雖然這是個人的見解，

我認為 DRAGAN 訓練於 Getchu 資料集是較好的，

因為人物看起來較為清晰，

雖然 DRAGAN 訓練於 safebooru 資料集有些人物也是不錯，

但更多的時候是生出來的圖片看起來已經不像是個人了，有點崩壞了。

接著是 SNGAN 所生成的結果，此處同樣為 350 個 Epoch 的結果，

### SNGAN 訓練於 safebooru 資料集
![](/assets/img/2021-01-16-GAN_illustration_dataset/SNGAN.png)

### SNGAN 訓練於 Getchu 資料集
![](/assets/img/2021-01-16-GAN_illustration_dataset/SNGAN-Getchu.png)

比起 DRAGAN， SNGAN 的整體結果看起來是更糟的，

但是在 safebooru 的資料集中 SNGAN 所生成的人物圖片是較少有崩壞的結果的，

不過在 Getchu 的資料集中 SNGAN 生成的還是較差。

# 結論

本次對了兩個動漫資料集進行訓練，

分別使用了兩個不同的 GAN 模型來學習是否可生成動漫圖片。

在觀察實驗結果後認為 Getchu 因圖片的多樣性較少，

所以模型可以學得較為好，

但這也說明了未來該模型所生成的變化種類也會限縮在該範圍。

用另一種觀點來看，

如果未來可以對 safebooru 的資料集再收集更多的圖片，

並且訓練得更久使其收斂的話，

其結果或許會不一樣，

但這就是要考量時間與運算成本問題了。


另一個在實驗中觀察到的結果，

Discriminator 與 Generator 的互相訓練是 GAN 框架中最重要的一環，

但實務上來說當 Discriminator 可以完全區別出 Generator 所生成之圖片與真實世界圖片的時候，

此時 GAN 整體的訓練就會崩壞，

因為 Discriminator 會迫使 Generator 無法學習。

如同這篇論文 [TOWARDS PRINCIPLED METHODS FOR TRAINING GENERATIVE ADVERSARIAL NETWORKS] 所說的 。

此部分雖透過 Lipschitz 來制約 Discriminator 不要越來越強，

但效果卻不明顯。

整體來說目前還不確定模型生成的圖片是否具備多樣性，

雖說其多樣性應該會取決於模型的能力，

但仍不確定該如何評估整體模型的能力，

或許可視化模型是一個可行的方向吧，

不過這部分由於還在研究中～

最後如果有任何的建議，

請讓我知道。

# 實驗環境

OS : Ubuntu 16.04 LTS (64-bit)

CPU : Intel(R) Core(TM) i5–4590 CPU @ 3.30 GHZ

GPU : NVIDIA GTX970

Memory : 8GB


# 參考資料：


[GANを用いたイラスト生成のデータセット]

[Towards the Automatic Anime Characters Creation with Generative Adversarial Networks]

[DRAGAN]

[SNGAN]

[DCGAN]

[Lento]

[Lento]:https://medium.com/@crosssceneofwindff

[GANを用いたイラスト生成のデータセット]:https://medium.com/@crosssceneofwindff/gan%E3%82%92%E7%94%A8%E3%81%84%E3%81%9F%E3%82%A4%E3%83%A9%E3%82%B9%E3%83%88%E7%94%9F%E6%88%90%E3%81%AE%E3%83%87%E3%83%BC%E3%82%BF%E3%82%BB%E3%83%83%E3%83%88-f2a9171e7ec5
[Towards the Automatic Anime Characters Creation with Generative Adversarial Networks]:https://arxiv.org/pdf/1708.05509.pdf

[DRAGAN]:https://arxiv.org/pdf/1705.07215.pdf
[SNGAN]:https://arxiv.org/pdf/1802.05957.pdf
[DCGAN]:https://arxiv.org/pdf/1511.06434.pdf

[TOWARDS PRINCIPLED METHODS FOR TRAINING GENERATIVE ADVERSARIAL NETWORKS]:https://arxiv.org/pdf/1701.04862.pdf