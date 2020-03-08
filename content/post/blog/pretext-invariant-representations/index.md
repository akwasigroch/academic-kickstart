---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Self supervised learning with pretext invariant representations"
subtitle: ""
summary: ""
authors: []
tags: []
categories: ['Blog']
date: 2020-03-08T22:38:09+01:00
lastmod: 2020-03-08T22:38:09+01:00
featured: false
draft: false
links:
  - name: url
    icon_pack: fab
    icon: github
    name: Code
    url: 'https://github.com/akwasigroch/Pretext-Invariant-Representations'



#
# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: True

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
commentable: true
share: false
#math: true
---
## Introduction
Recently, I have started to experiment with self-supervised learning techniques. In the last few months, we can see the rapid development of new methods. Moreover, self-supervised learning is reaching transfer learning performance.

This family of methods allows using of unlabeled data to increase the performance of the task (e.g. classification, detection or so on). To better understand the idea behind those algorithms visit [here](https://arxiv.org/pdf/1902.06162.pdf) or [here](https://github.com/jason718/awesome-self-supervised-learning). Self-supervised learning is used not only in computer vision domain, here is an example of successful use of self-supervised learning in automatic speech recognition https://ai.facebook.com/blog/wav2vec-state-of-the-art-speech-recognition-through-self-supervision/

In this post, I focus on the paper [Self-Supervised Learning of Pretext-Invariant Representations](https://arxiv.org/pdf/1912.01991.pdf) by Ishan Misra, Laurens van der Maaten from Facebook.

## Idea
Self-supervised learning usually involves preparing a pretext task. For instance, apply rotation to the images, then train the network to predict the rotation angles. Thus, pretext task most often becomes a simple classification task. The advantage of this approach is that we do not need to have manually assigned labels. We can generate labels automatically. The pretext task training aims to obtain good quality image representations. So, after pretext task pretraining, we can train our network on a downstream task (our target task) using those representations. This approach is similar to transfer learning that also involves two stages – training on big dataset then training on a target task.

In the mentioned paper authors also perform data augmentation. However, they do not focus on the prediction of the properties of the transformation. Instead, they try to make the representation of the original image and a modified image similar. This makes sense. For example, the image of the dog should produce the same feature vector regardless of the rotation angle. This can lead to more robust computer vision algorithms.


{{< figure src="dog.67.jpg" title="These two images should produce similar representations" lightbox="true" >}}


How to make the network produce the same representations? By proper design of the loss function.
So the idea is to pass both the original image and modified image by the network. Then change the network parameters in the optimization process to make the representation of two images similar. It can be obtained by minimizing the distance between those two representations. Unfortunately, there is one problem. The network can learn the trivial solution, for example, return zeros for all examples. To avoid such solutions, the distance between the modified image and other images in the dataset should be maximized. This lead to the loss function of the following form:

$$ h(\mathbf v_{\mathbf I}, \mathbf v_{\mathbf I^{t}}) = \frac{\exp( \frac{s(\mathbf v_{\mathbf I}, \mathbf v_{\mathbf I^{t}})}{\tau})}{\exp( \frac{s(\mathbf v_{\mathbf I}, \mathbf v_{\mathbf I^{t}})}{\tau}) + \sum_{I^{'} \in \mathcal{D}_{N}} \exp( \frac{s(\mathbf v_{\mathbf I^{t}}, \mathbf v_{\mathbf I^{'}})}{\tau}) } $$



A detailed explanation of the function is provided in the paper. The numerator is a distance between the image and its modification, while the denominator is a sum of distances between the modified image and other images in the dataset. What is important, this function is similar to the softmax function. Therefore, we can use the cross-entropy loss function available in deep learning libraries.

You can notice the representations of other images in the delimiter. Obtaining those every batch could be very costly, so the authors decided to construct a representation bank that contains representations of all images in the dataset. So instead of calculating that every time we can look up the array.

The authors made an extensive analysis of many tasks. I'm not going to describe this here. What is interesting, the authors managed to achieve better image detection performance using self-supervised learning instead transfer learning.

## Implementation

I decided to implement the algorithm in the Pytorch library. The code is available here: https://github.com/akwasigroch/Pretext-Invariant-Representations. I performed initial experiments on the ISIC 2017 dataset. The dataset contains 2000 training images, 150 validation images, and 600 test images. I obtained the following results on ResNet50 network:
- 0.55 AUC - training from scratch (the network can't learn)
- 0.70 AUC - training using a self-supervised pre-trained network
- 0.80 AUC - training using transfer learning from a network trained on Imagenet dataset

I did not manage to reach the performance of transfer learning. However, the results are promising, and I think an increase in the number of images (I used the same images in supervised and self-supervised training) can lead to much better results. This is especially important in medical cases where many images are not labeled, as it is time-consuming and involves the work of a skilled medical specialist.




