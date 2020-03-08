---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Self supervised learning with pretext invariant representations"
subtitle: ""
summary: ""
authors: []
tags: []
categories: ['Blog']
date: 2020-02-19T22:38:09+01:00
lastmod: 2020-02-19T22:38:09+01:00
featured: false
draft: true
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
  caption: "Reconstructions of images sampled from $p(x|z)$ distribution"
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
commentable: true
share: false
---
## Introduction
Recently, I have started to experiment with self-supervised learning techniques. In last few months we can see the rapid development of the methods and new methods of the state of the art. Moreover, self-supervised learning is closing the gap reaching transfer learning performance.

This family of methods allows to use of unlabeled data to increase the performance of the task (e.g. classification, detection or so on). There are many methods, and great reviews, if you are more interested visit https://arxiv.org/pdf/1902.06162.pdf and https://github.com/jason718/awesome-self-supervised-learning. The topic is not only computer vision domain, here is the example of utilizinf such methods in automatic speech recognition https://ai.facebook.com/blog/wav2vec-state-of-the-art-speech-recognition-through-self-supervision/

In this post I focus on the paper pretext invariant (...)

## Idea
Many self-supervised learning task involve preparing of pretext task. In many cases the standard approach is for example performing data augmentation and then predicting the parameter of those modification. For example, we rotate the image, and the network have to predict the rotation angle. Thus, this pretext task become simple classification task. The big advantage of that, is that we do not need have manually assigned labels. We can generate labels automaticly. After such training, we can train our network on downstream task (our target task).

In the paper i descrive today, authors also perfome data augmentation. However, they state that we have not to predict the parameter of modification. Instead we have to make original image and augmented image have similar representation. This make sense. For example the image of the dog should produce the same feature vector regardless of the angle of a dog


(photo)


How to make the same representations? By properly performed loss funtion.
So the idea is to pass the original image and modified image by the network. Then change the network parameters in optimization process to make the representation returnet from both two vector the same. Great, but there is one <te>. It can learn trivial solution, for example return zeros for all examples. Therefore, we also make sure, that the original image is similar to modified version of that image while make the representations far away from other images. To measure the distance between representations they used cosine similarity that return the cosinus of angle between two vectors. 
So the loss function become:
**loss function**

They state in the paper that it comes to maximize that. Hovewer, if we maximize that function, ale the probabilities comes down. To better understand that see the analogy of that h function to softmax function. If we maximize one class, we minize prob of the rest class at the same time. This analogy also helps as because of we can use stanrd cross entropy loss in impelmentation.

You can notice the representations in delimiter, obtaining it every batch could be very costly, so they decided to construct representation bank that contains representations of all images in the dataset. So instead of calculating that every time we can look up the array.

They slightly modified loss function. Firstly, the compare representations from memory to represeation of original image, that dampen the change of parameters. MOreover, modified loss allows for making original image and unomidfied image from database not similar.

They made extensive analysis on many taks. I'm not going to describe this here. WHat is interesting, the authors managed to beat the object detection task agains transfer learning.

## Implementation

I decided to implement the algorithm in Pytorch library. The code is available here: https://github.com/akwasigroch. I performed initial experiments on the ISIC 2017 dataset. Dataset contains 2000 training images, 150 validation images and 600 test images. I obtained following results on ResNet50 network:
- 0.53 AUC - training from scrath (the network can't learn)
- 0.71 AUC - training using self-supervised pretrained network
- 0.81 AUC - training using transfer learning from network trained on Imagenet dataset

I did not managed to reach the performance of transfer learning. Hovewer, the results are promising, and I think that increase in the number of images (I used the same images in supervised and self-supervised training) can lead to much better results. This is especcialy important in the medical cases, when there are many images that are not labeled, as it is time consuming and involve the work of skilled medical specialist.




