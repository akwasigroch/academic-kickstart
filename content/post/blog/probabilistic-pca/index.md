---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Learn about probabilistic PCA to better understand Variational Autoencoders"
subtitle: ""
summary: ""
authors: []
tags: []
categories: ['Blog']
date: 2020-02-05T22:38:09+01:00
lastmod: 2020-02-05T22:38:09+01:00
featured: false
draft: false
links:
  - name: url
    icon_pack: fab
    icon: github
    name: Code
    url: 'https://github.com/akwasigroch/probabilistic_pca'



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

I prepare a presentation on Variational Autoencoders to my university colleagues. I think what can help them understand the topic better. What cause a problem in Variational Autoencoders understanding is a derivation of the loss function as it involves intuition and knowledge of probabilistic concepts.

I think that understanding probabilistic PCA first helps to understand Variational Autoencoders faster. Probabilistic PCA is a great and simple algorithm. The big advantage of this algorithm is that all the probability distributions used in the algorithm are Gaussian distributions. So starting from the latent variable distribution $p(z)$ and distribution of observed variables conditioned on latent variable $p(x|z)$, we can easily obtain the distribution of observed variable $p(x)$, and posterior distribution $p(z|x)$. It could be obtained analytically by known equations. What's more, we can easily sample from that distribution using known libraries like Numpy or Scipy. So to gain the intuition for this algorithm, it is worth to play with the examples. 

I prepared the short implementation of the probabilistic PCA method. I plan to show the code to listeners on my presentation to make the algorithm easier to understand. I hope that it also helps you. The code is available at my Github:

https://github.com/akwasigroch/probabilistic_pca 

I plan to prepare longer text on my blog concerning that method.




