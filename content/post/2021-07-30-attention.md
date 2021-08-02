+++


title = "What on earth is attention"
description = ""
date = "2021-07-30"
tags = ["deep-learning"]
slug = "what-on-earth-is-attention"

summary = "Teaching myself about the attention mechanism in vision models"

+++

> **Warning**: I encourage you to not read this if you're looking for super formal jargon. I might've used some loose terms within this post which are just to build an intuition. 

## Intro

I like to see the rise of attention to be a next step after moving from linear layers to convolutions for vision tasks. 

Linear layers did not do a great job at capturing spatial (2D) information, this was solved by convolutions. But the problem that remained was that convolutions is that they still are very far from the way we humans perceive images. 

When trying to identify a dog from an image, the human does not look at every part of the image equally. Instead humans tend to focus on certain features like tails, fur, noses, etc. 

On the contrary, convolutions tend to look at every part of an image and give them "equal" importance. Which is not ideal. 

## Why do we need it ? 

When training a model, we dont necessarily want a model to give equal importance to every part of an image (thats what `conv2d` does). One way of accomplishing this is through trainable attention mechanisms.

**What is trainable attention ?**

A _trainable attention_ mechanism is trained while the network is trained, and is supposed to help the network to focus on key elements of the image.

There are primarily 2 types of attention in vision models: 
- hard attention
- soft attention 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/2021_july_30/hard_vs_soft_attention_samoyed.png" width="40%">}}

In our case, we'll stick with soft attention, because it is differentiable (unlike hard attention which is equivalent to cropping). 

## How does it work ? 

At the heart of it is the aptly named "Attention mechanism". It can be thought of a form of an approximation of a [`SELECT`](https://youtu.be/OyFJWRnt_AY?t=707) from a database.

It mimics the retrieval of a value `v` given a query `q` and a key `k`.

The attention mechanism does just this, but in a more fuzzy/probabilistic way.

There are 3 steps involved: 

1. A query (`query`) is assigned, which is then compared to the keys (`keys`)
2. The key `k_i` that matched query (`query`) is then selected
3. An output value `v_i`  (which is the same as `database[k_i]`) is returned 

This is how the pseudocode might look like: 

```python
class Attention():
    def __init__(self, similarity_fn):
        self.similarity_fn = similarity_fn

    def forward(self, query, keys, values):

        output = []

        for i in range(len(keys)): 
            output.append(self.similarity_fn(query, keys[i]) * values[i])

        return output

```

Now let's make a super simple attention layer with a binary similarity function:

```python
def my_similarity_function(query, key): 
    if query == key: 
        return 1.
    else:
        return 0. 

a = Attention(similarity_fn = my_similarity_function)

q = 1
k = [0,1,2]
v = [8,3,4]

print(a.forward(query = q, keys = k, values = v))
```

this would show the same result: 

```
[0.0, 3.0, 0.0]
```
If you paid attention so far (pun intended), then you'd realise that this similarity function is not differentiable. Hence we cannot use it to backpropagate and update the parameters. 

**So which similarity function should we use to make the process differentiable ?**

There are some functions we can consider for a more "continuous" measure of similarity: 
* The dot product or a scaled dot product
* Additive similarity

What you saw so far was not a real attention layer, it was just a caveman version of the real thing. It's about time that we move on to learn the real thing now :)


## Let's take a closer look

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/2021_july_30/attention_summary.png" width="80%">}}

Here's a quick breakdown of the diagram shown above: 

- **Similarity function**: Given a query `q` and a set of keys `[k0, k1, k2, k3]`, the similarity function calculates the similarity between the query `q` and each of the keys as `[s0, s1, s2, s3]`. Ideally, if `q` is very similar/equal to a key (say `k3`), then the corresponding similarity value tends to be `1.`, and if they're not at all similar then the similarity should be close to `0.`. 

- **Softmax**: Intuitively speaking, the softmax function converts the given values into a probability distribution.

- **Multiplying with values**: Here we multiply the outputs from the attention layer with the values and obtain the desired outputs. You can think of this as multiplying the values with a "mask" so that the model can focus more on certain parts of the values. 


## How can we use it in vision models ? 

Let's first have a look at the code, and then we'll see how everything works: 

```python
import torch.nn as nn
import torch 

class SelfAttention(nn.Module):
    """ Self attention Layer"""
    def __init__(self,in_dim,activation):
        super(SelfAttention,self).__init__()
        self.chanel_in = in_dim
        self.activation = activation
        
        self.query_conv = nn.Conv2d(in_channels = in_dim , out_channels = in_dim//8 , kernel_size= 1)
        self.key_conv = nn.Conv2d(in_channels = in_dim , out_channels = in_dim//8 , kernel_size= 1)
        self.value_conv = nn.Conv2d(in_channels = in_dim , out_channels = in_dim , kernel_size= 1)
        self.gamma = nn.Parameter(torch.zeros(1))

        self.softmax  = nn.Softmax(dim=-1) #

    def forward(self,x):
        """
            inputs :
                x : input feature maps( B X C X W X H)
            returns :
                out : self attention value + input feature 
                attention: B X N X N (N is Width*Height)
        """
        m_batchsize,C,width ,height = x.size()

        """
        generating query
        """
        proj_query  = self.query_conv(x)
        proj_query = proj_query.view(m_batchsize,-1,width*height).permute(0,2,1) # B X CX(N)


        """
        generating key
        """
        proj_key =  self.key_conv(x).view(m_batchsize,-1,width*height) # B X C x (*W*H)

        """
        getting similarity scores with dot product
        """
        similarity_scores =  torch.bmm(proj_query,proj_key) # transpose check

        """
        passing similarity scores through a softmax layer
        """
        attention = self.softmax(similarity_scores) # BX (N) X (N) 

        """
        generating values
        """
        proj_value = self.value_conv(x).view(m_batchsize,-1,width*height) # B X C X N

        """
        obtain outputs by multiplying values with attention scores
        """
        out = torch.bmm(proj_value,attention.permute(0,2,1) )

        """
        reshape to original shape [N, C, H, W]
        """
        out = out.view(m_batchsize,C,width,height)
        
        """
        multiplying outputs by a learnable parameter gamma and adding the input itself
            - the multiplication most probably is done to scale the outputs 
            - the input itself is added in so that it works sort of like a residual layer
        """
        out = self.gamma*out + x
        
        return {
            'output': out,
            'attention': attention
        }
```


## so are we breaking up with `conv2d`?

When convolutional layers started getting used for vision, did we completely ditch linear layers ? No. Same goes here. `conv2d` is here to stay, but it might not be as dominant in vision as it used to be. 

## Resources 

- https://arxiv.org/pdf/1804.02391.pdf
- https://github.com/SaoYan/LearnToPayAttention
- https://deepai.org/machine-learning-glossary-and-terms/softmax-layer
- https://discuss.pytorch.org/t/attention-in-image-classification/80147/3
- https://youtu.be/OyFJWRnt_AY
- Bonus link: https://youtu.be/T78nq62aQgM



