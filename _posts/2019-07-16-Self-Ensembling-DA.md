---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: Self-ensembling DA簡介 - Self-ensembling for visual domain adaptation


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  Geoffrey French, Michal Mackiewicz, Mark Fisher. ["Self-ensembling for visual domain adaptation"](https://arxiv.org/abs/1706.05208). In ICLR'18.

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
ICLR 2018 Paper

Github Code : https://github.com/Britefury/self-ensemble-visual-domain-adapt

Paper link : https://arxiv.org/abs/1706.05208

# 簡介

此文針對 Domain Adaptation 的任務做探討，

在 Domain Adaptation 的任務中會有兩個資料集 Source 及 Target Domain，

Source Domain 有圖片(Xs)以及標註(Ys - GT)，

而 Target Domain 只有圖片(Xt)沒有標註，

希望能夠藉由 Domain Adaptation 的方式，

讓模型能在 Target Domain 也能表現良好，

難處是因為 Source Domain 可能是合成的資料集，其顏色、外觀和現實世界的圖片有著落差。

所以直接套用至真實世界的資料集 - Target Domain 時準確度往往會下降很多。

而本文提出 Self-ensembling 的架構就是為了緩解這問題所提出的 Domain Adaptation 方式。

Self-ensembling 主要是透過改良 Semi-supervised 的方法，

> NIPS'17 - [Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results]

![](/assets/img/2019-07-16-Self-Ensembling-DA/fig1.png)



# 架構

![](/assets/img/2019-07-16-Self-Ensembling-DA/fig2.png)

基本上就是承襲 NIPS'17 - [Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results]，

整體的訓練方法與 Mean teachers 那篇論文差不多，

只是架構調整為應用於 Domain Adaptation 的任務，

將原本的架構結合 Target Domain 的部分，見上圖可明白差異。

Self-ensembling 主要是有兩個部分
- Student => 輸出稱作 z
- Teacher => 輸出稱作 z^

我們會使用 Source Domain Dataset 對 Student 的輸出(z)進行 Supervised 的訓練 - Cross-entropy loss(Supervised)，

再使用 Source 以及 Target Domain Dataset，

希望兩個模型能夠輸出一致的結果(z 及 z^) - Self-ensembling loss(Unsupervised)，

Mean-square-loss 用來確保兩個輸出要相似。

Note: 
> 1.其訓練過程為輸入同一張圖片(x)至兩個相同架構的模型，雖然輸入同一張圖片，但輸入至 Student / Teacher 時會經過不同的 batch normalize, droupout, noise, image augmentation 等等的設定。

如果對這部分想了解的更深入的可以看 [Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results]。

## Confidence thresholding

如果 Teacher 預測出的結果 probability < 0.968，

就將 Self-ensembling loss 設定為 0，

概念為當預測機率不夠高的話，

我們沒辦法確保預測出來的結果是準確，

可能會造成反效果，所以就不會給學生進行訓練。

## Data augmentation

- 水平翻轉 
- 圖片位移 [-2, +2]
- 高斯雜訊

實驗結果是 水平翻轉 以及 圖片位移 對準確度有比較顯著的提升。

圖片加入高斯雜訊的公式，

整篇論文唯一的公式呢。。。

![](/assets/img/2019-07-16-Self-Ensembling-DA/eq1.png)


## Class balance loss

主要是為了解決 SVHN 的圖片資料集中，

有某個類別過多的問題。

對每個 Target domain 的輸入 - N張圖片，

簡單來說就是看預測出來每個類別的機率，

如果那一個 batch 的某個類別機率太高，

就給懲罰 balance loss，

會將這 loss 當作權重如 0.75 乘上 self-ensembling loss。


# 成果

![](/assets/img/2019-07-16-Self-Ensembling-DA/fig3.png)

# 參考資料：
[Self-ensembling for visual domain adaptation]

[Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results]

[Self-ensembling for visual domain adaptation]:https://arxiv.org/abs/1706.05208

[Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results]:https://arxiv.org/abs/1703.01780
