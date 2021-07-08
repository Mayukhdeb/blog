+++


title = "Eden"
description = ""
date = "2021-05-26"
tags = ["deep-learning", "generative-art"]
slug = "eden"

summary = "Building the building blocks for generative art"


+++


# What is [Eden](https://github.com/abraham-ai/eden) ? 

I first got to know about eden through one of the many meetings with Gene Kogan. He envisioned it as a "place where everyone is innocent". This was in context to abraham.ai. 

In a nutshell, eden would be a way for people to easily host generative art pipelines with minimal modifications to the original code. It would also provide abraham.ai with a generalised framework to port new pipelines into the website. 

Eden bridges the gap between the generative artist's hacky code and a well hosted server backend. 

## Envisioning

I want to see eden as a framework which can be used to convert anyones code to a servable pipeline with minimal additions/deletions to the original code. 

Evolution taught us that we either have to be fast or hard to survive. Eden chooses to be fast. What I mean by fast is that eden should be the fastest way to port new art pipelines into a server to host it on a website. 

Eden should be a place where artists are not afraid to go wild with their ideas, without worrying about the "worldly problem" of hosting it.

Every unit within eden is a `BaseBlock`, it is somehting that takes in an input (or "DNA" as Gene Kogan called it) and returns an output. Every `BaseBlock` can be hosted with a simple `host()` method. 

Ideally, one should be able to mix and match blocks (like running style transfer on a CLIP+VQGAN text to image pipeline). But that's something which we can work on after we have an MVP ready for the twitter flex. 

```python
@eden_block.run(args = my_args)
def do_something(config): 
    # do something here
    return something

eden_block.host(
    port= 5656,
    max_num_workers= 4  ## max 4 threads
)
```

## A breath of fresh air 

The more I read about the idea, the more I felt like I should be a part of it. But this meant that I would have to go out of my usual pytorch zone to something completely different. 

Thankfully I wasn't starting from scratch. I already had hacked together a few fastAPI model serving pipelines before, so I knew that it would help. 

## Not so fresh bugs 

Like any other project, eden too has had a fair share of ugly bugs, which were dealt with. This was the first time I was working with tools like celery. 

I realised that I did not just like deep-learning. Building stuff is what I really liked. Deep-learning was just one way to do it. 

## Looking ahead

Eden has not been fully built yet, but I plan to use this blog post as a way to track myself here through it's genesis :)


