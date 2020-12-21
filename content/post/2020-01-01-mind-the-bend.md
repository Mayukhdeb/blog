+++


title = "Mind the bend"
description = "training a car to drive by itself using deep learning"
date = "2020-01-01"
tags = ["Computer vision", "Deep learning", "Self driving car"]
slug = "mind_the_bend"

summary = "Making a car drive by itself using deep learning and some PyAutoGUI"


+++


![example](https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/example.gif)
![pipeline](https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/pipeline.png)

The whole point of this project was to make a car stay on the road all by itself and defeat other players in a racing game called speed dreams.

## Collection of training data

* Rapidly takes screenshots of the game and saves them in a folder
* The label in this case is the x-co-ordinate of the mouse which is captured by pyautogui and is stored in the formatted filename of each collected image. Image fileame formatting is done as  ```(x-value)_(unique_ID).png```

## Processing images

* Converts all images to numpy arrays with a depth of 3 for R,G and B color channels 
* The shape gets changed from  ```[ width, height, depth ]``` to ```[ depth, width, height]``` for it to be of the right size for the CNN input channel
* Pairs them with the corresponding mouse x-coordinate as array ```[  [[[image]]]  ,  [x-value]   ]```
* all of the generated pairs are then stacked into one numpy array with the help of np.vstack() and saved 

## Data preprocessing and augmentation

* First things first, plotted the frequency distribution of each steering value hen
* Roughly doubled the amount of training data by generating mirror images of existing images and vstacking them with reversed steer value.
* Flattened the frequency distribution by making copies of random ramples of under-represented steering value data points
* Normalised the steering values by replacing the x-co-ordinates with steering values. In my case the "straight" steer value was at ```x = 400```, for ```normalised_value = 400 - x_value```

> note :  Right is negative steer value and left is positive

## Self driving

* Rapidly taken screenshots are prerpocessed and fed to the trained CNN drunk_driver()
* ```drunk_driver()``` returns a steer value
* Returned value is taken care  of by pyauogui which moves the mouse accordingly
