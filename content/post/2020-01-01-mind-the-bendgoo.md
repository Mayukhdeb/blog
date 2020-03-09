+++


title = "Adventures with augmentation"
description = "Exploring and experimenting with microscope imagery datasets."
date = "2020-02-01"
tags = ["Deep learning", "Image dataset", "object detection", "CNN", "data augmentation"]
slug = "adventures-with-augmentation"

summary = "Exploring and experimenting with microscope imagery datasets."


+++



 

[![Binder](https://camo.githubusercontent.com/bfeb5472ee3df9b7c63ea3b260dc0c679be90b97/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f72656e6465722d6e627669657765722d6f72616e67652e7376673f636f6c6f72423d66333736323626636f6c6f72413d346434643464)](https://nbviewer.jupyter.org/github/Mayukhdeb/adventures-with-augmentation/tree/master/)


## Parallel CNNs work just as good as they look

![parallel_cnns](https://raw.githubusercontent.com/Mayukhdeb/adventures-with-augmentation/master/parallel_cnn.jpg)


### But why use them anyways ?
* Because when two different architectures are trained on the same training set, they don't have the same weaknesses (i.e different confusion matrices) 
* This means that when both are combined, they tend to neutralise each other's weaknesses, which gives us a boost in accuracy.

## Class activation heatmaps on deep neural networks

![comma](https://raw.githubusercontent.com/Mayukhdeb/adventures-with-augmentation/master/sample_images/original_comma.png)


![comma](https://raw.githubusercontent.com/Mayukhdeb/adventures-with-augmentation/master/sample_images/heatmap.png)


![comma](https://raw.githubusercontent.com/Mayukhdeb/adventures-with-augmentation/master/sample_images/superimposed.png)

* Shows the regions of the image which gave the most activations for a certain label in a trained classification model


* In simpler words, it tells us about the regions of the image which made the model decide that it belongs to a certain label "x"

![comma](https://raw.githubusercontent.com/Mayukhdeb/adventures-with-augmentation/master/sample_images/cell_detect_pipeline.png)



* And when the heatmap is superimposed upon the real image, it gives us an insight on how the CNN "looked" at the image
