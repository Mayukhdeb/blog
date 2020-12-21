+++


title = "The \"EPIC\" dataset"
description = "Google summer of code week 4"
date = "2020-06-06"
tags = ["GSoC", "OpenWorm", "deep learning"]
slug = "gsoc-2020-june-7"

summary = "Google Summer of Code | Coding period week 1"


+++


## [Exploring the metadata](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/embryo_analysis/metadata_exploration.ipynb)

So, the week started with me shifting my focus from movement analysis to developmental biology. Which made me stumble upon the [EPIC dataset](http://epic.gs.washington.edu/). The website contained a lot of videos capturing the process of embryogenesis in the C. elegans embryo.  The best part was that each video was accompanied with time tagged metadata with columns like: 

* `x` and `y` co-ordinates of the cell

* Cell name (more on that below)

* Time value of when the data point was captured. 

Plotting the movements of some of the cells gave an interesting insight on how a certain type of a dwells in a certain part of the embryo. 

 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/movement_vis.png" width="40%">}}

 ## [Deep learning on the metadata](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/embryo_analysis/deep_learning_on_metadata.ipynb)

 The first objective was to make sense of what the cell names meant, and the diagram shown below summed it up pretty well. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/lineage_diagram.jpg" width="40%">}}

But there was a new problem now, it was that there were more than 700 unique cell names with a highly unbalanced distribution through the dataset. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/compact_cellname_explain.png" width="40%">}}


The solution was to "shorten" the cell names to only the first few characters. This would give us info about the cell's lineage (or the name of the cell itself, depending on the value of `time`) and not necessarily it's precise name. This shortened `compact_cellname` would now be used as a label for the deep learning model. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/pie.png" width="30%">}}


This reduced the number of labels to 32, but there was still a lot of imbalance (as seen above), which was taken care of by the [`RandomOverSampler()`](https://imbalanced-learn.readthedocs.io/en/stable/generated/imblearn.over_sampling.RandomOverSampler.html).

The next step was to train a model on the data, and with the help of an adaptive learning rate which only reduced when the loss plateaued (check - [`ReduceLROnPlateau()`](https://pytorch.org/docs/stable/optim.html#torch.optim.lr_scheduler.ReduceLROnPlateau)), the simple 5 layer model reached a a 90% accuracy after 30 epochs of training. 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/lr.png" width="40%">}}
{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/june_7/training_curve.png" width="40%">}}

The model can obviously be improved in the coming weeks with more data and a better architecture. 



