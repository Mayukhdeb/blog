+++


title = "Visualizing adversarial examples with caricatures"
description = ""
date = "2021-05-26"
tags = ["deep-learning", "feature-vis"]
slug = "adv-example-caricatures"

summary = "Breaking down adversarial examples with carictaures"


+++

## Two important terms: "adversarial examples" and "caricatures"

### 1. Adversarial examples

In simple words, an adversarial example is an image which has been engineered to "fool" a neural network, while being visually indifferent from that of a natural image to the human eyes. 

The simplest way to do this is to use the FGSM algorithm: which is basically "training" the image to maximize or minimize the activations of a certain class within the neural network. 

Adversarial examples are proof that even though deep neural nets seem non linear, their linear nature at a smaller scale (`y = wx + b`) can be very easily exploited. 

To know more about adversarial examples, you might out to [check this out](https://www.youtube.com/watch?v=CIfsB_EYsVI&ab_channel=StanfordUniversitySchoolofEngineering).

### 2. Caricatures

Ever seen cartoons of famous people drawn in a way to exaggerate their features (eyes, nose, etc) ? They're called caricatures. 

They're an exaggerated version of what one sees. 

**How are caricatures relevant here ?**

Imagine a 2 layer neural net with 2 layers `P` and `Q`

So the model looks like: 

```python
class model(nn.Module):
    def __init__(self, p, q):
        self.P = p  ## p and q are imaginary layers
        self.Q = q

    def forward(self, x):
        '''
        x_p is an encoded version of x
        '''
        x_p = self.P.forward(x)
        x_q = self.Q.forward(x_p)

        return x_q
```

So now let us ask this question:

**Is there a way to reconstruct the input image given a layer encoding `x_p` ?**

The answer is yes, let me explain: 

First let us feed an input image `X_original` into the model, and save the encoded version as `X_original_encoded`.

Now let us prepare a trainable image parameter and call it `X_trainable`. 

After feeding `X_trainable` into the model, we get an encoded version of it called: `X_trainable_encoded`

Since `X_trainable` and `X_original` are not the same,`X_original_encoded` and  `X_trainable_encoded` are also not the same. 

Which means we can simply make them more similar by minimizing the loss between `X_trainable_encoded` and `X_original_encoded`. 

**So which loss function should be used ?** ?

Initially I went for the MSELoss, but that did not provide much insight about the layer itself. 

Then after some very interesting discussion with Ludwig from Distill Slack, I went for cosine similarity. 

**What on earth is cosine similarity now ?**

Imagine you're looking up at the sky during a solar eclipse. 

you  --  moon  --- sun

How far is the moon from the sun ? *very far*

Therefore, the MSE loss between the position of the sun and the moon is very high.

But how far do they *look* to the humans on earth ? *very close, sometimes even on top of each other*

Hence, the cosine similarity between their positions (with respect to earth as the origin) is high i.e 1. 

Given 2 points, cosine similarity is calculated as the cosine of the angle between them w.r.t the origin.

If the angle between 2 points is high, then the similarity is low: `cos(59) = 0.559`

If the angle between 2 points is low, their similarity is high: `cos(10) = 0.9848`

I really don't like to use fancy mathematical terms, so if you're that kind of a person [I'll link you to this article](https://deepai.org/machine-learning-glossary-and-terms/cosine-similarity).

**That was quite astronomical tangent, now let's get back to caricatures**

(to be continued)
