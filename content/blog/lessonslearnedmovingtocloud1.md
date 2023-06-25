---
title: My Journey in Moving an On Premise Cluster to the Cloud 1/N
description: My Journey in Moving an On Premise Cluster to the Cloud 1/N
date: 2023-06-25
tags: software engineering
---

# My Journey in Moving an On Premise Cluster to the Cloud 1/N
Wordy enough title, am I right?

Over the past two years, I have had the opportunity to design and develop all the infrastructure and tooling required to transfer an on-premise data center
to a distributed computing monster in Azure, utilizing their CycleCloud technology. Journey with me as I detail the blood, sweat, and tears that go into
leading a project like this. I'll go through why this was even considered in the first place, infrastructure requirements for each, getting CEO/CIO approval, building out the real thing with testing, into initial production and beyond. This was a real test of grit and character, and I'm excited to share this story with you all. I don't know how many parts this will contain, but I can't possibly fit it all into one - so I've deemed this 1/N. I hope it is captivating enough to follow along with the story!

## Why move the data center, anyway?

Good question. I'll start by saying this next part isn't the responsibility of my team, but these are things I learned along the way. 

When you purchase big compute nodes, you buy them from some vendor at a large scale. When you purchase them, you can also purchase something called a 
support contract, where the vendor will help you troubleshoot any hardware / software failures directly related to their technology. However, for a given
set of hardware, that support contract has a lifetime based on the expected lifetime of the resource. For example, if you purchased nodes that were expected to last
two years before burning out, the support contract would likely last two years. Our previous nodes were under contract for five years, and the five years was
about to run out. Basically, this hardware was end of life which requires a complete refresh.

A refresh means that the institution would need to buy a whole new set of compute nodes, and these would need to be justified by some serious metrics to support the cores
and RAM that were to be purchased in the refresh. We were at the refresh, and my manager at the time needed to make a case for the compute we needed for our production
workload. So he gathered up all the numbers, and found a very interesting trend...

### A mutually exclusive problem

Imagine you're a chef and you make pizzas for a living. Like any good pizza chef, you only cook your pizzas in a pizza oven. Your pizzeria is open 6 days a week, and it is absolutely poppin' - but only on Fridays and Saturdays. On Fridays and Saturdays, you have more customers than you do pizza ovens, often leading to huge wait times for your delicious pizza. You realize with all your weekend customers you can afford to buy some new pizza ovens! Good on you. This allows you to handle those peak times a little better, but what about those other pesky days where you aren't that busy? Your pizza ovens sit idle most of the day... and this isn't great. You wish there was some way you could have all the pizza ovens you need on the weekend, but during the week you could rent those pizza ovens out to other pizza chefs who may be in need of them.


<img alt="pizza_ovens_by_time.png" src="https://github.com/drkennetz/drkennetz.github.io/blob/main/content/img/pizza_ovens_by_time.png?raw=true" data-hpc="true" class="Box-sc-g0xbh4-0 kzRgrI">


This fun little pizza oven graph shows two things:

1. Pizza chef does not have enough ovens to deal with peak demand.
2. For a good portion of time, the pizza ovens are barely being used.

So this leads to the mutually exclusive problem we faced with compute in our data center. We would receive the majority of our data heading into the weekend. We only had 1300 cores available, and sometimes these data peaks would require nearly 10X that, so we'd have a tremendous amount of pending time for a lot of those data processing jobs. However, after that peak was processed, the 1300 cores largely remained idle - they weren't doing any work. The dilemma my manager found was this:

1. The cores over the time average of a year were being utilized about 7% of the time - IE 93% of the time they remained idle with no data processing jobs.
2. During peak times, jobs would pend for several hours, and the cores would be occupied at nearly 10X their capacity (13,000 jobs for 1,300 cores)
3. We could not solve both the efficiency and capacity problems on hardware we owned.

We would either need to buy more hardware to meet peak demand which would decrease utilization leading to more waste (the more likely scenario), or we would need to buy less cores to increase percent utilization decreasing waste but increasing pending time - ultimately decreasing throughput (no one would go for this from a data processing perspective).

So this led us to investigate the third option, which was to move to an elastic computing model in the cloud. This model would have a different infrastructure than an on-prem data center model and would be faced with its own challenges, but could potentially solve both problems. 

In the next post, I'll talk about the difference between the on-prem HPC infrastructure and the distributed computing infrastructure in the cloud, and the advantages and disadvantages of both models, as well as how the cloud could be a potential solution to our mutually exclusive problem. I hope you've enjoyed this weekend's post and you'll follow along next week!

Thanks for reading,

Dennis



