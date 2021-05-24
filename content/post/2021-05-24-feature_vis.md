+++


title = "A Few Months With Feature Visualization"
description = "Learning to reverse engineer CNNs and other stuff"
date = "2021-05-24"
tags = ["deep-learning"]
slug = "feature-vis"

summary = "Learning to reverse engineer CNNs and other stuff"


+++

## What on earth is feature visualization ? 

Just like how we train neural networks on images, feature visualization works by training "images" to achieve objectives generally associated with neural networks. 

One of the primary goals has been to try and train images to activate certain parts of CNNs to gain an understanding of how they work or more precisely: "what they're looking for".

## Training images ? 

Yes, just like any other NN out there, images are also a bunch of numbers. So they can be "trained" through gradient descent algorithms. 

#### Isn't training images to activate features the same as generating adversarial examples with fancy objectives ? 

In the early days of feature visualization, people tried a wide variety of approaches to make the feature visualizations to **not** look like adversarial examples. 

The problem was not with the training process, neither was it with the models themselves. But it was in the way we handled our trainable images. 

If we simply "train" an image with someting like `torch.optim.Adam` (say), then all that we'd get is a bunch of high frequency noise which does achieve the objective of activating the desired parts of the neural net, but it has no "visual information" for human eyes to percieve and understand. 

The problem with the vanilla approach is that they're do not contain any information interpretable by human eyes.

## Fixing the "gradient descent/ascent" approach

I'll list my favourite ones below (not in any order): 

* **DeepDreams**: In simple words deepdream does the following: "if it looks like a monkey, make it look more like a monkey." 

* **Using GANs as an image parameter**: This process gave natural looking images, but is not recommended because it depends a lot on the GAN that we're using. A GAN trained on birds would not be able to visualize a class `dog`. 

* **FFT parameterization**: This is the state-of the art approach as of now. Instead of telling the trainable image to "change this single pixel to red", it tells "change this small area of pixels to red". This has led to much more natural looking patterns/visualiations. 

The FFT parameterization approach is my favourite because it makes the trainable image think in terms of waves/blobs instead of pixels. 

We humans don't draw by making millions of small dots on the paper, instead we use brushes to "fill spaces" with colors. FFT image parameterization is an attempt at doing just that, but in computers.


I might add more stuff into this blog post from time to time :) 