+++


title = "Exploring the Movement Database"
description = "Google summer of code week 2"
date = "2020-05-24"
tags = ["GSoC","Computer vision", "Deep learning", "OpenWorm", "Feature extraction"]
slug = "gsoc-2020-may-24"

summary = "Google Summer of Code | Community bonding period week 2"


+++



After week 1, the new priority was to find the right data on the internet. I started the week off by reading papers based on locomotion in the C. elegans worm. Surprisingly enough (or unsurprisingly, depending on how much you're into worms), most of the data was from the [OpenWorm Movement Database](http://movement.openworm.org/).

Almost all the video data on the database is from the [Tierpsy tracker](https://github.com/ver228/tierpsy-tracker), which is an amazing worm tracking tool in itself. But there was a new hurdle now.

 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_24/initial_image.png" width="60%">}}


The issue with the video data (as seen above) wThe issue with the video data (as seen above) was that the worms were "partially segmented out". Which meant that the background was all blacked out except for a small "padding area" around the worm. This "padding area" was a good safety measure on the Tierpsy tracker's side, but was a minor hurdle  for someone who wanted to segment out just the worm from the image in order to gain inferences from behavioural dynamics.

This hurdle was taken care of in 3 stages:

 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_24/OW_movement_database_pipeline.png" width="100%">}}

1. Stage 1: Extracting the "padding region" around the worm with [`cv2.threshold()`](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_thresholding/py_thresholding.html)

2. Stage 2: Removing the padding region by using [`cv2.bitwise_and()`](https://docs.opencv.org/master/d0/d86/tutorial_py_image_arithmetics.html) between the real and the thresholded image

3. Removing any extra noise (as seen on stage 2) with [`cv2.erode()`](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_morphological_ops/py_morphological_ops.html)

Now the next objecive was to extract the head and tail, this was done  using corner detection with [`cv2.goodFeaturesToTrack()`](https://docs.opencv.org/2.4/modules/imgproc/doc/feature_detection.html#goodfeaturestotrack) with `maxCorners = 2` to only capture the 2 most prominent corners. 

 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_24/head_and_tail.png" width="60%">}}

 To complete the skeletonization, all that was needed now was to skeletonize the main body, and that was done with the very self explanatory function named [`skimage.morphology.skeletonize()`](https://scikit-image.org/docs/dev/auto_examples/edges/plot_skeleton.html). Combined with two circles which signify the head and the tail, we get a clean skeleton which can now be used for analysis on a bigger scale with a large number of frames.


 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_24/skeleton.png" width="60%">}}
 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_24/skeleton_long.png" width="60%">}}







