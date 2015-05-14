---
layout:     post
title:      Accessing Princeton's cluster
date:       2015-05-14 09:33:00
summary:    Notes on how to use della
categories: snippets
---

To log onto della first log on to nobel

```
ssh efuller@nobel.princeton.edu
```

From there can log into della

```
ssh efuller@della.princeton.edu
```

`\tigress\efuller` is where I should keep my scripts, write out data to

`nobel` are entry linux servers for beginners.

`qstat -u efuller` to check status of jobs

`sbatch run_job.sh` to run the job
`qdel jobnum` to kill a job that's running

Always change `ntasks` first before `N` in the `run_job.sh` code.

Also see [Bridgett's wiki](http://openwetware.org/wiki/VonHoldt:High_Throughput_Sequencing_Resources
) on using the research computers:

`scancel jobID` will cancel a job.

Also some scp commands to get the models back down from `della` via `nobel`

```
scp model_* efuller@nobel.princeton.edu:/n/homeserver2/user2a/efuller

scp efuller@nobel.princeton.edu:/n/homeserver2/user2a/efuller/model_* .
```

```
scontrol show jobid 1109549
```
Gives start time


Runtime notes

Square matrix: how many entries, times the number of bytes per entry (8 because it's real). 12 GB is about half a node. Submit it to the test node (runtime less than one hour). Then run `scontrol` to see which node it's on.

Then ssh to node
````
ssh della-r1c3n5
```
Then run `top` to see how much memory it's using.
