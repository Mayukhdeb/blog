+++


title = "PCA and semantic segmentation"
description = "Google summer of code week 5"
date = "2020-06-11"
tags = ["GSoC", "OpenWorm", "deep learning", "semantic segmentation", "PCA"]
slug = "gsoc-2020-june-14"

summary = "Google Summer of Code | Coding period week 2"


+++

Week 2 has been the most satisfying week in GSoC yet, with a lot of things getting done. Let's have a look at them one at a time:


## [Starting out with semantic segmentation](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/tree/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/embryo_segmentation/)

This is one of the primary objectives of my GSoC project, and it's about time that I started working on it. 

The primary data source would be the [WormImage Database](https://www.wormimage.org/), and the objective is to train a model which can segment out the nuclei from the  image of an embryo of the C. elegans worm. 

The problem is that there's no labelled segmentation data, only raw images. But I've been trying to figure out a way through it, which involves:

1. Taking an original image and manually drawing over the desired parts which are to be extracted. I had to come up with a [simple drawing tool](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20(DevLearning)/notebooks/embryo_segmentation/drawing_tool_for_manual_labelling.ipynb) which could be used to generate the training masks. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_14/drawing.png" width="60%">}}

2. Run both the original image and the mask through a heavy (but practical) [image augmentation pipeline](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/embryo_segmentation/augmentation_prototyping.ipynb) (generate at least 50 images from one)

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_14/augment.png" width="60%">}}


3. Repeat steps 1 and 2 for a few images to get a larger dataset to train on.

I already did steps 1 and 2 on one such image of an embryo and generated a few augmented samples as seen above.

## [Principal component analysis on embryo metadata](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/embryo_analysis/principal_component_analysis.ipynb)

This is where I used some tools like:

* `StandardScaler().fit_transform(x)` standardizes the features by removing the mean and scaling them to unit variance. In simpler words, it will transform the data such that its distribution will have a mean value 0 and standard deviation of 1.

* `sklearn.decomposition.PCA(n_components = 2)` helped in decomposing the 4 dimensional data ( `x`, `y`, `size` and `time`) into a 2 dimensional space while conserving as much information as possible. 

Even though 100% of the information from the 4D space could not be projected into the 2 principal components, it gave us a good idea on how cells with similar (or same) lineage ancestors lied closer to each other in the 2D space composed of the 2 principal components. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_14/PCA.png" width="60%">}}

