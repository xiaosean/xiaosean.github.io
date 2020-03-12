---
# Posts need to have the `post` layout
layout: post
comments: true

# The title of your post
title: 偵測視線目標簡介 - Detecting Attended Visual Targets in Video


# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
description: >
  Eunji Chong, Yongxin Wang, Nataniel Ruiz, James M. Rehg. [Detecting Attended Visual Targets in Video](https://arxiv.org/abs/2003.02501). In CVPR'20.
 
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
CVPR 2020 paper

Paper link: https://arxiv.org/abs/2003.02501

Github(目前還未新增-請關注此連結): https://github.com/ejcgt/attention-target-detection

# 簡介

此任務是針對影片中的各個目標進行視線/關注目標的偵測，

透過此模型可得知影片中各個人物所關注的對象，

主要應用於社交場合的視線追蹤。

![](/assets/img/2020-03-12-att-detect-tgt/fig1.png)

該模型提出利用圖片理解和人物頭像之間的交互關係來增進模型在影片中的辨認率，

並且能處理目標的視線對象不在圖像內的狀況。

此外還提出新的資料集 VideoAttentionTarget(於 2020.03.12 未搜尋到，等作者發布，之後應該 Github 連結會有) 現實世界的視線追蹤資料集。

下圖為 VideoAttentionTarget 資料集的示意圖

![](/assets/img/2020-03-12-att-detect-tgt/fig2.png)

此模型驗證在 GazeFellow, VideoAttentionTarget 以及 VideoCoAtt 這三個資料集中。



# 方法

整體架構是延伸該作者的前作 [Connecting Gaze, Scene, and Attention: Generalized Attention Estimation via Joint Modeling of Gaze and Scene Saliency] 作為延伸，改良為影片視線追蹤並可處理目標不在圖片內的判斷機制

![](/assets/img/2020-03-12-att-detect-tgt/fig3.png)

整體可以將影片的視線追蹤大致分為三個部分，對應到論文的架構為
- 頭像識別 - Head Condition Branch
- 圖片理解 - Main Scene Branch
- 影片時序性理解 - Recurrent Attention Prediction Module

整體概念：首先提取出頭像的位置，將該頭像的特徵萃取出來(Head Condition Branch)，獲得頭像轉向的方向之類的動作特徵，再結合頭像位置資訊，可獲得視線所關注的熱力圖(較不準確)。接著再與整張圖片理解之特徵作結合(Main Scene Branch)，即可獲得視線目標的位置(較準確)，對於影片來說增加時序性的理解(Recurrent Attention Prediction Module)可獲得更好的準確率。

# 頭像理解 - Head Conditioning Branch
這部分的概念是提取出目標頭像，萃取出頭像部分的特徵作為動作理解，可看到上方模型圖片的下半部。

這邊特別之處是加入 Head position 黑白圖片(黑代表頭像位置，白代表其餘部分)提取出頭像位於圖片的位置(細節可看論文)，並與 Head feature map 做結合，輸出 Attention map，獲得可能圖片中可能是視線位置的熱力圖(見下圖 Learned soft-attention weight)。

Note: 此處的  Attention layer 是兩個 Fully-connected layer。

![](/assets/img/2020-03-12-att-detect-tgt/fig4.png)

# 圖片理解 - Main Scene Branch
此處將 Head position 與 Scene image 做為輸入，Head position 提供頭像位置資訊，讓模型可作為關注的參考，最終再將 Scene feature map 與 Attention map 相乘，藉此可讓圖片理解之特徵與熱力圖所推估的可能做結合。

# 影片時序性理解 - Recurrent Attention Prediction Module
這部分就是結合 Conv-LSTM 讓模型可以依序透過每個 Frame 所預測之視線位置來校正出較為準確的視線位置，最後再透過 Deconv 來獲得原本的圖像大小。

# 視線目標是否在圖片內的判斷機制 - Heatmap modulation
此部分是透過學習出一個值 α 來判斷說視線目標是否在圖片內，這部分會與最終的 Feature map 做修正，細節去看論文。

# 成果

![](/assets/img/2020-03-12-att-detect-tgt/fig5.png)

![](/assets/img/2020-03-12-att-detect-tgt/fig6.png)

![](/assets/img/2020-03-12-att-detect-tgt/table12.png)

# 參考資料：

[Detecting Attended Visual Targets in Video]

[Connecting Gaze, Scene, and Attention: Generalized Attention Estimation via Joint Modeling of Gaze and Scene Saliency]

[Detecting Attended Visual Targets in Video]:https://arxiv.org/abs/2003.02501

[Connecting Gaze, Scene, and Attention: Generalized Attention Estimation via Joint Modeling of Gaze and Scene Saliency]:https://arxiv.org/abs/1807.10437

