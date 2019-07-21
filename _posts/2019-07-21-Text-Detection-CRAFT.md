---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: CRAFT簡介 - Character Region Awareness for Text Detection


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  Youngmin Baek, Bado Lee, Dongyoon Han, Sangdoo Yun, Hwalsuk Lee. [Character Region Awareness for Text Detection](https://arxiv.org/abs/1904.01941). In CVPR'19.

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
CVPR 2019 paper

Paper link: https://arxiv.org/abs/1904.01941

Github(Pytorch): https://github.com/clovaai/CRAFT-pytorch

# 簡介

此論文針對文字偵測的任務進行研究，

而文字偵測的問題套用到現實世界時會發現，

現實世界的文字(word)可能是彎曲、不規則的形狀，

以 Detection-based 的方法預測雖然已經能夠獲得不錯的準確度，

![](/assets/img/2019-07-21-Text-Detection-CRAFT/fig1.png)

但是對於文字過度變形，或是文字過大這種的狀況，

使用 Anchor 去實作的 Bounding box 方法還是沒辦法處理得很好這些 case，

為了改善這種狀況，

提出 CRAFT 框架 - Character Region Awareness For Text detection，

採用了 Character-level awareness，

本文提出先藉由測量出每個字元(character)，

再預測出每個字元是否是屬於同一個文字，

透過 Affinity 模組所給出的分數，

我們可以判定要將哪些的字元做串接，

最後輸出我們的結果 - 文字(word)。

因此此模型主要輸出兩個東西
- Region score - 該位置是否是字元
- Affinity score - 該位置的字元是否需要進行串接成文字

只是現存的真實世界的文字資料集的標注是 word-level，

只有將一張圖片拆成文字(word)，

並沒有將一張圖片拆成字元(character)的，

因為這樣標註的話成本太高，

此篇論文提出使用合成的字元資料集做訓練，

透過預測的方式測量出真實世界每個字的熱力圖，

再藉由 weakly-supervised 的方式訓練出一個可以適應於真實世界的字元偵測器(Character-level awareness)，

藉由此框架對於不規則形狀的文字或是文字過大的測資能達到較好的處理。

# 訓練資料處理

因為沒有 Character-level 的真實世界資料集，

因此會使用合成的 Character-level 的資料集，

下圖為 Character-level 的圖片範例，

![](/assets/img/2019-07-21-Text-Detection-CRAFT/character_box.png)

此文使用 Guassion 的熱力圖方式，

希望我們的模型可以對字元的中心有著較高的準確度，

因為字的框並非方正的，需要做變形。

![](/assets/img/2019-07-21-Text-Detection-CRAFT/score_generation.png)

透過這種方式我們可以得到一張圖片中每個字的熱力圖，

藉由這種當作我們的 GT 做訓練。

那 Affinity - 判斷字元是不是屬於同一個字，就比較麻煩一點，

我們要知道哪些字和字之間是怎麼關聯的，

![](/assets/img/2019-07-21-Text-Detection-CRAFT/affinity_box.png)

可以看到紅色線的部分，

我們會先將一個字分成4個藍色三角形，將相鄰兩個字的上下三角形的中點做串連，

藉由紅色的框線(Affinity box)來表示兩字元是有相關的，

同樣的會再將這框線轉換成 Guassion 熱力圖。

因此最終的架構如下圖

![](/assets/img/2019-07-21-Text-Detection-CRAFT/fig3.png)

# 架構

![](/assets/img/2019-07-21-Text-Detection-CRAFT/fig4.png)

對於合成資料集 Character-level，

我們可以進行 Supervised 的訓練，

![](/assets/img/2019-07-21-Text-Detection-CRAFT/eq3.png)
- *: Pseudo Ground Truth，透過我們資料前處理來的。
- Sr: Region score
- Sa: Affinity score
- Sc(p): 在合成資料集權重為 1

但現實世界的資料集只有 Word-level - 文字以及字數，

我們會將現實世界的圖片使用 Bounding box 萃取出文字的區域，

再經過字元偵測的模型，

偵測出字元的熱力圖再將它轉成字數，

依據字數是否相同給不同 Bounding box 的不同的訓練權重。

![](/assets/img/2019-07-21-Text-Detection-CRAFT/eq1.png)
- w: 真實世界 word-level 資料集的圖片
- l(w): 字數
- l 上標 c 代表是使用預測的方式得出的，

![](/assets/img/2019-07-21-Text-Detection-CRAFT/eq2.png)
- R(w): bounding box

## Loss Function

![](/assets/img/2019-07-21-Text-Detection-CRAFT/eq3.png)

合成資料集的 Sc 為 1，

現實世界 word-level 的資料集，

依據每個 bounding box 給予不同權重。

![](/assets/img/2019-07-21-Text-Detection-CRAFT/fig5.png)

我們可以看到一開始能辨認出來的字數有限，因此透過 eq 1 計算權重就會很小，

在這時候主要使用合成資料集來訓練，

隨著訓練到後來字數開始準確後，權重會越來越高，

這時候就能慢慢適應到真實世界的文字。

## Inference

簡單來說呢就是會有一個 binary mask 設為 0，

當 Region score > Threshold 或是 Affinity score > Threshold 就設為 1，

之後還要後處理，這部分不詳述。

- 對 Mask 做 Connected Component Labeling (CCL) - opencv 有函數是相似的功能 connectedComponents。
- QuadBox - opencv 有函數是相似的功能 minAreaRect。

# 成果

![](/assets/img/2019-07-21-Text-Detection-CRAFT/table1.png)

![](/assets/img/2019-07-21-Text-Detection-CRAFT/fig8.png)


# 參考資料：

[Character Region Awareness for Text Detection]

[Character Region Awareness for Text Detection]:https://arxiv.org/abs/1904.01941

