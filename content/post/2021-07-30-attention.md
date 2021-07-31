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

1. A query `q` is assigned, which is then compared to the keys `k`
2. The key `k_i` that matched query `q` is then selected
3. An output value `v_i`  (which is the same as `database[k_i]`) is returned 

This is how the pseudocode might look like: 

```python
def attention(q,k,v):
    """
    q: query
    k: keys
    v: values
    warning: this is just a simple dummy example with lists, its not vectorized
    """

    """
    initiating default zero values for output: 
    [0,0,0,0,0...]
    """
    out_values = [0 for i in range(len(k))]

    for i in range(len(k)): 
     
        """
        checking if our query matches one of the keys
        """
        if q == k[i]:

            """
            if a query matched a key, then put the corresponding value into the output
            """
            out_values[i] = v[i]
    return out_values
```

Let's take an example:

```python
q = 1
k = [0,1,2]
v = [8,3,4]

attention(q,k,v)
```

returns: 

```python
>>> [0, 3, 0]
```

If you paid attention so far (pun intended), then you'd realise that this process is not differentiable. Which is why this is **not** how it's done. 

We can better summarise the process seen above as: 

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

Here we replaced the initial function with a much more flexible class wrapper which can support many different types of functions to check if a query matches a key. Lets try to see how the initial function looks like in this new approach: 

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


## [To-do] Let's take a closer look

[under construction]

## so are we breaking up with `conv2d`?

When convolutional layers started getting used for vision, did we completely ditch linear layers ? No. Same goes here. `conv2d` is here to stay, but it might not be as dominant in vision as it used to be. 

## Resources 

- https://github.com/SaoYan/LearnToPayAttention
- https://discuss.pytorch.org/t/attention-in-image-classification/80147/3
- https://youtu.be/OyFJWRnt_AY
- Bonus link: https://youtu.be/T78nq62aQgM



