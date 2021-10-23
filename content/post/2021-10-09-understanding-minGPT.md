+++


title = "minGPT for dummies"
description = ""
date = "2021-10-09"
tags = ["deep-learning", "GPT"]
slug = "mingpt-for-dummies"

summary = "Trying to understand how Andrej Karpathy's minGPT works, step by step."

+++

> *First off, this is by no means an official guide/tutorial. If you're stalking me, then this post wont be super useful. If you're here by accident, then all that I can say is that life works in mysterious ways.*

I'll try my best to explain to myself and to you, the reader how [Andrej Karpathy](https://github.com/karpathy)'s [minGPT](https://github.com/karpathy/minGPT) works. All the source code you see here is taken (and maybe slightly modified) from the [minGPT repo](https://github.com/karpathy/minGPT).

# 1. Building a dataset 

```python
import math
from torch.utils.data import Dataset

class CharDataset(Dataset):

    def __init__(self, data, block_size):
        chars = sorted(list(set(data)))
        data_size, vocab_size = len(data), len(chars)
        print('data has %d characters, %d unique.' % (data_size, vocab_size))
        
        self.stoi = { ch:i for i,ch in enumerate(chars) }
        self.itos = { i:ch for i,ch in enumerate(chars) }
        self.block_size = block_size
        self.vocab_size = vocab_size
        self.data = data
    
    def __len__(self):
        return len(self.data) - self.block_size

    def __getitem__(self, idx):
        # grab a chunk of (block_size + 1) characters from the data
        chunk = self.data[idx:idx + self.block_size + 1]
        # encode every character to an integer
        dix = [self.stoi[s] for s in chunk]
    
        x = torch.tensor(dix[:-1], dtype=torch.long)
        y = torch.tensor(dix[1:], dtype=torch.long)
        return x, y
```

So what's going on inside `__init__` ? 

First, lets look at the arguments.

* `data`: a long, long string which contains all of the training data. In this case it would be something short like that of a poem. 
* `block_size`: number of characters to be included in the input/label. One can also think of it as the spatial extent of the model for its context.

And now the attributes: 


* `self.stoi`: weird name, which actually means "`str` to `int`"
* `self.itos`: weird name, which actually means "`int` to `str`"
* `self.vocab_size` is the total number of unique characters found within the training data. 

if you print `self.stoi`, it looks like: 

```python
{'\n': 0,
 ' ': 1,
 "'": 2,
 '(': 3,
 ')': 4,
 ### its a lot longer IRL, I clipped it out :)
}
```

`self.itos` is basically the same thing but flipped around: 

```python
{0: '\n',
 1: ' ',
 2: "'",
 3: '(',
 4: ')',
 ### its a lot longer IRL, I clipped it out :)
}
```

In a nutshell, they're just a way to map all of the unique characters in the dataset to certain integers. 


Now let's build a dataset: 

```python
block_size = 10 ## 10 is not a good number for training, but is good for intuition 
text = open('input.txt', 'r').read() 
train_dataset = CharDataset(text, block_size) 
```

Let's look at a single training sample:

```python3
x, y = train_dataset.__getitem__(0)
print('x: ', x)
print('y: ', y)
```

the output would be something like: 

```python
x:  tensor([10, 28,  1, 17, 18, 16, 17,  1, 27, 14])
y:  tensor([28,  1, 17, 18, 16, 17,  1, 27, 14, 10])
```

here, each number in `x` and `y` points to a certain character in the training dataset. Let's see what `x` and `y` actually are: 

```python
def ints_to_readable(train_dataset, x):
    x_readable = [train_dataset.itos[i.item()] for i in x]
    return(''.join(x_readable))

print('x: ', ints_to_readable(train_dataset, x))
print('y: ', ints_to_readable(train_dataset, y))
```

output: 
```
x:  at high se
y:  t high sea
```

That's all for the dataset! before we move on to the model, here's another interesting point mentioned in the [original notebook](https://github.com/karpathy/minGPT/blob/master/play_char.ipynb):

*So for example if block_size is 4, then we could e.g. sample a chunk of text "hello", the integers in x will correspond to "hell" and in y will be "ello". This will then actually "multitask" 4 separate examples at the same time in the language model:*

- *given just "h", please predict "e" as next*
- *given "he" please predict "l" next*
- *given "hel" predict "l" next*
- *given "hell" predict "o" next*


# 2. The model

The next step would now be to look at the model's basic architecture, which depends mainly on the following factors: 

1. Vocabulary size of the dataset 
2. Block size of the dataset.
3. Number of layers 
4. Number of attention heads
5. Size of the embedding vector

The first 2 factors are already known from the training dataset. So let us look at the last 3 factors:

* Number of layers (`num_layers`): The number of standard repeated layers to be used in the model. Higher -> deeper model. 
* Number of attention heads (`n_head`): Check out [this section](https://mayukhdeb.github.io/blog/post/transformers-toolbox/#multi-head-attention) I wrote in another blog post
* Size of the embedding vector (`n_embd`): It can be thought of as a lookup table which takes in a bunch of integers as input and returns corresponding "vectors". 

**Wait, what are embeddings ?**

They're "learnable look-up tables", which can be constructed with `torch.nn.embedding`: 

```python
import torch.nn as nn

embedding = nn.Embedding(3, 4)
print('embedding values: \n', embedding.weight.data)

print(f'embedding([0]): ', embedding(torch.tensor([0])).detach())
print(f'embedding([1,2]): ', embedding(torch.tensor([1,2])).detach())
```

would print something like: 
```python
embedding values: 
 tensor([[ 1.5205, -2.2728, -0.0874,  0.4219],
        [-1.3103,  0.3491, -0.0410,  1.1601],
        [ 0.7829,  0.2559, -1.7153,  0.1395]])
embedding([0]):  tensor([[ 1.5205, -2.2728, -0.0874,  0.4219]])
embedding([1,2]):  tensor([[-1.3103,  0.3491, -0.0410,  1.1601], [ 0.7829,  0.2559, -1.7153,  0.1395]])
```

**Also, what is layer normalization ?**

Would recommend you to check out [this section](https://mayukhdeb.github.io/blog/post/transformers-toolbox/#layer-normalization) I wrote in another blog post.

## Components 

### Self attention:

I've already explained how self attention works in [another blog post](https://mayukhdeb.github.io/blog/post/what-on-earth-is-attention/), highly recommended that you check it out. 

The difference here (`CasualSelfAttention`) is that it is [has multiple heads](https://mayukhdeb.github.io/blog/post/transformers-toolbox/#multi-head-attention), with an extra linear layer in the end. 


### The transformer block: 

```python
class Block(nn.Module):
    """ an unassuming Transformer block """

    def __init__(self, config):
        super().__init__()
        self.ln1 = nn.LayerNorm(config.n_embd)
        self.ln2 = nn.LayerNorm(config.n_embd)
        self.attn = CausalSelfAttention(config)
        self.mlp = nn.Sequential(
            nn.Linear(config.n_embd, 4 * config.n_embd),
            nn.GELU(),
            nn.Linear(4 * config.n_embd, config.n_embd),
            nn.Dropout(config.resid_pdrop),
        )

    def forward(self, x):
        x = x + self.attn(self.ln1(x))
        x = x + self.mlp(self.ln2(x))
        return x
```

Let's first look at the GeLU actvation function.

```python
import torch
import numpy as np

def gelu(x):
    cdf = 0.5 * (1.0 + torch.erf(x / np.sqrt(2.0)))
    return x * cdf
```

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/2021_oct_9/gelu.png" width="50%">}}

It should be visible from the image above how GeLU is different from ReLU. But the real interesting difference lie in their derivatives 

{{< figure src="https://raw.githubusercontent.com/Mayukhdeb/blog/master/content/post/images/2021_oct_9/gelu_derivative.png" width="80%">}}

The image shown above is taken from [this blog post](https://alaaalatif.github.io/2019-04-11-gelu/) which is a great resource if you want to know more about GeLU.


# 3. Training

## Abstraction

First of, let us see what the model actually does from an input-output perspective: 

Given a sequence of characters, we first convert them to an embedding (a bunch of integers). For our example, we are assuming that the block size is 4. hence the length of the input along dim 0 is 4.

```python
## ['p', 'y', 't', 'h', 'o']
x = [2, 6, 7, 8, 4]
```

Given the input sequence as shown above, the model has to predict the next "step" of the sequence. Something like: 

```python
y = model(x)
y ## ['y', 't', 'h', 'o', 'n']
>>> [6, 7, 8, 4, 3]
```

In reality, the output is of shape: `[Block size, Vocabulary size]`. So if the vocab size was 9, then the ideal output `[6, 7, 8, 4, 3]` is actually:

```python
## shape: (4, 9) i.e (Block size, Vocabulary size)
[
    [0, 0, 0, 0, 0, 0, 1, 0, 0], # 6
    [0, 0, 0, 0, 0, 0, 0, 1, 0], # 7
    [0, 0, 0, 0, 0, 0, 0, 0, 1], # 8
    [0, 0, 0, 0, 1, 0, 0, 0, 0]  # 4
    [0, 0, 0, 1, 0, 0, 0, 0, 0]  # 3
]
```

Then if we run argmax over this output, we get `[6, 7, 8, 4, 3]`.

## Loss

The nature of the outputs of the model points to the fact that we're "classifying" the next sequence of words from an input sequence. 

For classification problems, we use the cross entropy loss. [This blog post](https://ml-cheatsheet.readthedocs.io/en/latest/loss_functions.html#cross-entropy) is a great resource in case you're not familiar with it. 

The special thing to notice here is that this is not your regular classification problem. Instead it is actually `n`
 classfication problems stacked up (where `n` = block size). 

 Hence the loss function looks like: 

```python
import torch.nn.functional as F

'''
block_size: 10
vocab_size: 33
'''
x = torch.tensor([1,2,3,4,5,6,7,8,9,0]).unsqueeze(0)
targets = torch.tensor([2,3,4,5,6,7,8,9,0,22])

'''
forward pass, you can ignore the ", _" for now
'''
logits, _ = model(x) # logits.shape: torch.Size([1, 10, 34])

'''
reshaping logits from (batch_size, vocab_size, block_size) to (batch_size*vocab_size, block_size)
and then calculating the cross entropy loss w.r.t targets
'''
loss = F.cross_entropy(logits.view(-1, logits.size(-1)), targets.view(-1))  
print(loss)
# >>> tensor(3.4539, grad_fn=<NllLossBackward>)
```
# 4. Inference