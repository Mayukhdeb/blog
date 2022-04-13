+++


title = "tmux for dummies"
description = ""
date = "2022-04-12"
tags = ["linux"]
slug = "tmux-for-dummies"

summary = ""

+++

Imagine that you're a 21 y/o student in a hostel with a really bad wifi. On top of that, you're bound by the timings of the lunch and dinner, and you cannot just leave your laptop open while you're out because then your very near and dear friends would mess with your laptop's `.bashrc`.

What you need, is a way to start long running scripts on your remote machine via ssh and log out without stopping the it, so that you can check back in later on.

This is exactly what tmux does for you.

To start a tmux session: 

```
tmux
```

then run whatever stuff you want to run within the session. 

After that, you can exit the session with: `ctrl B + D`

When you want to log back in, you might first want to list down the active tmux sessions:

```
tmux list-sessions
```

then, to attach to session 0 (for example), you can do the following.

```
tmux attach -t 0
```

That's it, that's the blog post. I'll probably keep coming back here because I always keep forgetting these commands. 

