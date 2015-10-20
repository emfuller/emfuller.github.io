---
layout:     post
title:      Figuring out parallel
date:       2015-07-30 09:00:00
summary:    Beginner notes on running code in parallel
categories: parallel snippets
---

On the slow trail of figuring out how to run things in parallel. Right now stuck on jargon. CPUs and cores, possibly the same thing? Regardless, [to figure out how many CPUs can use terminal](http://stackoverflow.com/questions/1715580/how-to-discover-number-of-logical-cores-on-mac-os-x).

```
sysctl hw.ncpu
hw.ncpu: 4
```
On mine I get 4, which is the number of CPUs that are available to run jobs. This is twice the number of physical CPUs that are actually on my computer. To find that number

```
sysctl hw.physicalcpu
hw.physicalcpu: 2
```

And to check that indeed I'm seeing logical cores (cores that are available due to hyperthreading)
```
sysctl hw.logicalcpu
hw.logicalcpu: 4
```

In this I've just assumed that CPUs and cores are interchangeable. Which is maybe not the case?

Alright, figured this out thanks to [this](http://stackoverflow.com/a/19314303/3137323) stack-overflow question.

> A CPU may have one or more cores to perform tasks at a given time. These tasks are usually software processes and threads that the OS schedules. Note that the OS may have many threads to run, but the CPU can only run at a given time X such tasks, where X = number cores * number of HW-threads per core. The rest would have to wait for the OS to schedule them whether by preempting currently running tasks or any other means.

So I think I can infer that I have 2 threads per physical core, which results in 4 logical CPUs above.

What happens then if I assign a parallel job that has more slaves than cores?
