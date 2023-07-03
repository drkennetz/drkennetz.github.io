---
title: My Journey in Moving an On Premise Cluster to the Cloud 2/N - Standard vs Distributed Computing
description: Standard vs Distributed Computing 2/N
date: 2023-07-03
tags: software engineering
---

In the last edition, I spoke about the mutually exclusive problem you face when owning your own hardware, and the initial motivations for us to investigate a cloud-based solution. Read about it [here](https://dksjourney.com/blog/lessonslearnedmovingtocloud1/)!

In this article, I initially planned on covering details about on-premises vs cloud high performance computing, but as I began writing I realized that there were additional topics that needed covered prior to making that jump. So today, I will focus on the differences between standard and distributed computing, which should lead into high performance computing and high throughput computing, which fall under the distributed computing domain.

## What is standard computing?

Before we discuss more advanced computing models in this and later posts, let's refresh on how standard computing works. Standard computing processes data via serial computing - workloads divide into a series of steps, and then the computer executes those steps one after another on the same processor. Like humans have a [longterm memory vs working memory](https://senecalearning.com/en-GB/blog/long-term-memory-vs-working-memory/), computers have disk storage for longterm access, and random-access memory (RAM). These are the standard locations the processor reads and writes data from / to. So if we relate a computer to a human, the processor is the brain, the disk is the brain, and RAM is the brain...

Okay, I admit that wasn't helpful. 

Let's instead use the analogy of moving houses. We will revisit this throughout the post. When we move, consider all the tasks we need to perform:

1. We must pack all our belongings into boxes
2. We must load all the boxes onto a truck
3. We must drive to our new home
4. We must unload the boxes
4. Sometimes we must repeat steps 2-4
5. We must unpack all the boxes into the appropriate places in our new home

In the standard, serial computing case, one person would do each of these things until they are complete and then move onto the next thing:

<img alt="single_person_moving_house.png" src="https://github.com/drkennetz/drkennetz.github.io/blob/main/content/img/single_person_moving_house.png?raw=true" data-hpc="true" class="Box-sc-g0xbh4-0 kzRgrI">

You might say, "yeah, but we have parallel computing! I could do all these tasks in parallel because we have multi-processing now!" And you'd be correct! This task of moving a house is a good example of a task that can be perfectly parallelized, if done correctly. When I use the term "perfectly parallel", I am referring to the state where we have resources to do all of these things with a maximized amount of resources available, and each of these steps can use all of the resources to 100% efficiency.

A perfectly parallel example of the above might mean that we have 5 friends available to help us move, and each wants to get done as quickly as possible because moving sucks - so we might as well get it over with. So with yourself and 5 friends, the above would look like:

1. 6 people pack all our belongings into boxes
2. 6 people evenly load boxes onto 6 moving trucks
3. 6 people drive to our new home
4. 6 people unload boxes
5. 6 people unpack all the boxes into the appropriate places in our new home

Now that's what I'm talkin' about! That seems way more effective than serial computing... and it is! When tasks can be parallelized, we get the same thing done much more quickly. Now, there is also the case where only part (or parts) of the process can be parallelized, so we still get increased throughput, but it isn't quite perfect. This would likely be the case in the moving scenario, because most people wouldn't rent 6 moving trucks to move a single house. They'd rent one, and they may need to make an additional trip or two, which would slow everything down. This is an important thing to consider when designing compute processes, and even systems!

In this instance, parallel computing seems like the solution to our problem. However, what if instead of moving a house, we were moving our entire 10 story office building into a new, 20 story office building because demand is booming? This would be a tall task for me and my 5 friends...

## What is Distributed Computing?

We have discussed standard computing and at the end of the last section we eluded to the fact that this may fall short of the situation where we need to do a very large job. Often enough, it isn't that standard computing couldn't handle a large task like "moving an office building" it would just take forever. This is often the case with data too (although oftentimes hardware limitations do in fact prevent standard computing from dealing with large volumes of data). 

So here we introduce distributed computing. In distributed computing, components of the system are shared among multiple networked computers or nodes. In this model, the individual components of the system interact with one another over a network (or networks) in order to achieve a common goal. There are numerous types of distributed systems, and the designs for each really should correspond to the data being processed, and the product being delivered. The pieces should be setup in a way to maximize the efficiency of the system, not any individual part. 

If we go back to our model of moving the office building for a moment, let's focus in on one component - loading the boxes into the truck and driving them to our new building. For the sake of our example, let's position our new office to be an hour away from our current office. Consider the following two cases:

1. My friends and I load up a truck that is already on site, and I drive the boxes to the new office location, and unload them.
2. My friends and I pack up the boxes and put them on a loading ramp, where one of many trucks will come. Each subsequent truck will arrive 30 minutes after the truck before it on a schedule, load the boxes, and drive them to the new office location, and unload them.

So when we take a look at the next section, let's discern how distributed systems optimize for throughput, rather than latency.

### Latency versus Throughput
Let's start with some definitions:

**Latency** in computer land refers to the amount of time it takes before a transfer of data **begins** following the **instruction** for its transfer. A human example of this is if I told my friend to pick up a box and put it in the truck, and it took my friend 15 seconds to move before beginning the task, the latency of that instruction would be 15 seconds.

**Throughput** simply refers to the quantity of data we can process in a given time. In our moving example, this is how many boxes we can get done in an hour.

I started with these definitions so we could adequately break down the points at the end of the distributed computing intro. The first scenario where my friends and I load the boxes, drive them, and unload them optimizes for **latency** - the time it takes us to do this task a single time will be much faster for a single truck than it will in the case for trucks arriving on schedule.

This is because in the schedule situation, we may finish getting all the boxes on the ramp 10 minutes after the previous truck leaves, meaning we would need to wait 20 minutes before the next truck even arrives. They would then need to load the truck before leaving, which would add additional time.

When my friends and I do it, we can **immediately** put the boxes on the truck, cutting out that loading time, and drive as soon as we get all the boxes out for a truck full. We could potentially cut down 25 minutes on that single task! 


<img alt="latency_vs_throughput.png" src="https://github.com/drkennetz/drkennetz.github.io/blob/main/content/img/latency_vs_throughput.png?raw=true" data-hpc="true" class="Box-sc-g0xbh4-0 kzRgrI">

We can see how this would be great if we had to do this a single time - we'd save 25 minutes! But when moving an office building, we'd likely do this tens to hundreds of times - and this is where optimizing for throughput comes in.

Using the same example as above, after unloading the boxes (which takes 10 minutes) we'd have to drive back an hour just to begin the next batch. We'd then take an additional 10 minutes to load the boxes, drive an hour back, etc. etc. And we'd be working all the time! 

This means for 2 complete truckloads of boxes, we'd need to take:

(10m to load) + (60m to drive to) + (10m to unload) + (60m to drive back) + (10m to load) + (60m to drive to) + (10m to unload) 

= 10+60+10+60+10+60+10 = 220 minutes

Contrast that to 2 truckloads for the schedule:

(10m to load) + (25m next truck + load) + (60m to drive) - but while this driving is taking place, the next truck is already in route, so do we really include the drive time? The answer is kind of... It looks a bit more like this:

(30m for truck1) + (60m drive - 30m for truck2 + 10 to unload) + (60m drive - 30m for truck3 + 10 to unload)... to N trucks:

= 30 + (60 - 30 + 10) + (60 - 30 + 10) = 110 minutes

Basically, we offset the cost associated with a single trip by always having many trucks circulating through, which eliminates that pesky time to drive back:

<img alt="trucks_in_circular_route.png" src="https://github.com/drkennetz/drkennetz.github.io/blob/main/content/img/trucks_in_circular_route.png?raw=true" data-hpc="true" class="Box-sc-g0xbh4-0 kzRgrI">

To summarize this section, when we have many trucks in rotation, the cost of doing this a single time is more expensive - IE it has higher latency. For every single instance of a truck taking this route, the latency will ALWAYS be higher with respect to that single truck. But when we consider the cost of the whole system, the throughput will also be much higher because the latency cost of a single truck is offset by distributing the workload to many trucks over many trips.

## Summary
This is how distributed computing systems work. The latency of a single operation between two separate components of the system **will** be higher than in standard computing because the operation will occur over a network. In standard computing, the two operations will occur on the same machine, so the latency is just the process communicating with another part of the same machine.

Standard computing will work fine up until a volume threshold - your computer can handle a fairly large number of tasks. But when we scale to thousands, then millions, then billions of operations, the throughput optimized distributed system wins out. 

In the next post, I'll talk about two different types of distributed systems used for performing analyses on large quantities of data - High Performance Computing (HPC) and High Throughput Computing (HTC). I'll touch on the advantages and disadvantages of each, and when they are appropriate to use. After painting this picture, I'll (hopefully) arrive at the decision which led our team to the cloud in an effort to solve our mutually exclusive problem (see last post for details).

As always, thanks for reading!

Dennis

