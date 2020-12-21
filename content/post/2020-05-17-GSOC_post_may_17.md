+++


title = "Hello Openworm"
description = "Google summer of code week 1"
date = "2020-05-17"
tags = ["GSoC","Computer vision", "Deep learning", "OpenWorm"]
slug = "gsoc-2020-may-17"

summary = "Google Summer of Code | Community bonding period week 1"


+++


  Ten weeks of proposal writing and 3 personal (but very beloved) deep learning projects later, I was selected for the only project I worked towards for GSoC, which was [Pre-trained models for developmental neuroscience](https://summerofcode.withgoogle.com/projects/#6078813261266944) under [INCF](https://www.incf.org/). And things have been better than ever since then.

The first week was mostly me getting to know more about the OpenWorm community and what are they striving to do. I got to meet [Dr. Bradley Alicea](https://github.com/balicea), [Vinay Varma](https://github.com/nvinayvarma189) and [Ujjwal Singh](https://github.com/ujjwalll). They gave me a warm welcome into the community.

I started setting up the environment right after the results were out, and cleaned up one of my [older repos](https://github.com/Mayukhdeb/adventures-with-augmentation) which would act as a quick reference through the span of this project.

The first weekly meeting with OpenWorm gave me a much better idea of the goals of this project.

 The key to a good deep learning model is good data. If the data is clean, half the battle is won. This led to the the first [notebook](https://nbviewer.jupyter.org/github/devoworm/GSoC-2020/blob/master/Pre-trained%20Models%20%28DevLearning%29/notebooks/worm_tracking_pipeline.ipynb) which builds a pipeline for tracking and segmenting the worms directly from video files. The pipeline works as follows:

  {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_17/may_17_visualization.png" height="900">}}


1. The video file is first trimmed to shorten the clip to extract only a short subclip (this is optional).

2. All the frames from the subclip are extracted from the video using [ffmpeg](https://zulko.github.io/moviepy/ref/ffmpeg.html) and saved into a folder in a numerical sequence of filenames.

3. The locations of the worms are determined by drawing padded rectangles around the contours of the thresholded image.


 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_17/rectangles.png" width="60%">}}


4. The original image is then cropped and segmented to remove all background except the worm(s) in the image. 


 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_17/segmented.png" width="60%">}}


Here's a GIF to visualise the tracking on the live feed. 


 {{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/may_17/tracking.gif" width="80%">}}


Apart from all of this, I also read quite a few papers on the C.elegans worm. This was an effort towards getting more familiar with the underlying biological concepts surrounding the worm.