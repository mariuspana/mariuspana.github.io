---
layout: post
title: "Linux performance - Wait IO"
excerpt: "Performance bottlenecks on linux due to IO Wait."
tags: [linux, monitoring, check_mk, performance]
image:
  feature: 
  credit: 
  creditlink: 
---

A while back we migrated one of our servers from a vmware esx platform to CentOS and KVM. Since the migration happened overnight we did not identify any issues, on the contrary I could have sworen that the server was performning better than before. Over the course of the next couple of days however a different picture appeared.

The server in question was not of critical importance however as all performance related issues go I was curious to know what was happening. We instrument all of our servers and services with the powerful [check_mk monitoring system ](http://www.sphs.ro/check_mk)so I pulled up the check_mk Multisite and looked at the server in question.

![](/images/io1.png)

It was immediately apparent that something was not right. This system was almost spending more time waiting on the CPU than actually processing stuff.

A further look at iostat and top made it abundandtly clear that we we should be looking the IO stack (disks, drivers, filesystems). Next I looked at the underlying virtual host to see what things looked like from that point of view.

![](/images/io2.png)

Here as well we see the CPU spending at least 10% of its time waiting. Lets take a closer look at disk IO to see if we can get a better understanding of what is going on.

![](/images/io3.png)

Interesting. Ive seen such patterns before on systems that have recurring workloads so I started by quickly looking at the system-wide cron jobs.

In hindsight there was an issue I had overlooked a day or two earlier: the server was checking its software RAID partitions and you the system felt overall a little sluggish.

There is a cron job named raid-check which runs once a week. While this may not be an issue for most, it is configured by default to run Sundays at 1am so it should not get in the way, there is the distinct possibility is you are using large SATA disks (3TB disks in our case) where the job would not finish in one weeks time.

The raid-check will try to do its job only when the disks are idle. This being a web and email server that served clients globally there was almost never an instant when that would be true.

While the raid-check should be enabled we had to identify a different schedule as it would almost always take more than a week to finish.

Issues like these are easily identifiable with a powerful monitoring solution such as [check_mk](http://www.sphs.ro/check_mk). We now have thresholds in place for just this type of scenario. I however hope to reduce these scenarios considerably by using a much better designed file system and hyprevisor.