+++


title = "Training the segmetation model"
description = "Google summer of code week 6"
date = "2020-06-21"
tags = ["GSoC", "OpenWorm", "deep learning", "semantic segmentation"]
slug = "gsoc-2020-june-21"

summary = "Google Summer of Code | Coding period week 3"


+++

The objective of week 3 was to train the model on a dummy dataset first (for prototyping) and then on a larger dataset. 

The three primary conponents of our training pipeline were:

* [PyTorch](https://pytorch.org/) for training and deploying the models. 

* [albumentations](https://github.com/albumentations-team/albumentations) for building the image augmentation pipeline. It was important for us to make sure that the image and the mask went through the exact

* [segmentation-models-pytorch](https://github.com/qubvel/segmentation_models.pytorch) to build Neural Networks for Image segmentation.


But now there was a problem with the augmented/resized images, the problem was that the pixel values were getting altered due to the resizing interpolations. This was fixed with a manual override with `mask[mask != 0] = 255`. It converted all the non zero values to 255 forcefully, just like it was before going through the augmentative transforms. 

Interestingly enough, I faced this problem even after using the manual fix. But this time it was because of the `transforms.resize` function within the `torchvision.transforms`. The problem was traced down to the interpolation, the very fact that the default interpolation was set to `Image.BILINEAR` (default arg) was the reason why the pixel values were getting altered in the mask. This was fixed by setting `interpolation = Image.NEAREST`.

After all of these transforms, the training images and masks came out to be as shown:
{{< figure src="/post/images/june_21/train_image.png" width="20%">}}

## The model

So the next step was to train the model on these images, and for our case, we used the [pre-trained ResNet18](https://supervise.ly/explore/models/res-net-18-image-net-2717/overview) as the encoder. Some of the other parameters used are as follows:
 
 * `ACTIVATION = 'sigmoid'` clamps the output pixel values between `[0.,1.]`, which are ideal for calculating a loss with respect to a mask whose pixel values also are within the same range. 

 * `DEVICE = 'cuda'` moves the model to the GPU for training.

 * `in_channels = 1` means the model takes grayscale images as input.


## The loss function 

The [**Intersection Over Union**](https://en.wikipedia.org/wiki/IOU) score (also known as Jaccard Index) is a statistic used for gauging the similarity and diversity of sample sets. It is defined as:

{{< figure src="/post/images/june_21/iou_score.png" width="30%">}}
{{< figure src="/post/images/june_21/iou.png" width="30%">}}

 [**Dice Coefficient**](https://en.wikipedia.org/wiki/S%C3%B8rensen%E2%80%93Dice_coefficient) can be seen as the percentage of overlap between the two sets, that is a number between 0 and 1. `DiceLoss()` can be mathematically defined as  `1 - dice_coefficient`:

{{< figure src="/post/images/june_21/diceloss.png" width="30%">}}

where `|X| ∩ |Y|`  is the intersection where the prediction correctly overlaps the label in the 2D mask. 

## Training metrics and visualizing the outputs

The model could hit an IOU score of about 0.7 after 24 epochs of training. 

{{< figure src="/post/images/june_21/training_curve.png" width="30%">}}

And when combined with some thresholding on the predicted images, here are the predicted results:

{{< figure src="/post/images/june_21/seg_results.png" width="50%">}}




