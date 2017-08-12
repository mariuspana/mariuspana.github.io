---
layout: post
title: "SGI UV not just for HPC"
excerpt: "The SGI UV shared memory server is all about performance and that does not mean strictly HPC."
tags: [hpc, sgi, windows, openstack]
image:
  feature: 
  credit: 
  creditlink: 
---

The SGI UV is an [extremely powerful machine](http://www.datacenterknowledge.com/archives/2012/06/14/hawking-is-first-user-of-sgi-big-brain-supercomputer/) and some consider it to be used only for the typical HPC workloads such as CFD, seismic processing, signals processing, etc., but we should remember the UV is based on [standard x86](https://www.sgi.com/products/servers/uv/features.html) so you can run off-the-shel Linux and do anything Linux on x86 operating environment can offer. This means small scale, large scale or extremely large scale.

![](/images/sgiuv2.jpg)


I often joke to partners and competitors alike from the hosting and telecommunications sectors that we could easily consolidate their entire cloud hosting footprint in just one SGI UV system. While I say it as a joke I am confident that I am not very far from the truth even for the largest ones. With an SGI UV system we can start with 4 sockets and build up to 256 sockets and 64TB RAM. That is 256 sockets and 64TB of RAM in a single operating environment (system state image). Who would not want the simplicity of the UV for their hosting and cloud computing needs compared to the alternatives: hundreds of stacked servers with clumsy interconnects, managing hundreds of operating environments with all their patching and updating? The UV is designed to be easy to use, manage and operate so it is a natural fit for the hosting and cloud business taking into consideration that it is much more versatile and can deliver on small workloads, large workloads and even HPC workloads effortlessly. 
For redundancy you can easily set-up multiple UV2 partitions to take care of backups, replications and even disaster recovery scenarios. You can even expand on this to offer disaster receovery in remote locations much more easily than you could with traditional clustered approaches.

Our friends over at [SGI](http://mariuspana.github.io/sgi-uv-not-just-hpc/www.sgi.com) moved a little quicker than I could and have posted the following video of the SGI UV running Windows 2012 on an OpenStack cluster. The configuration is called uv.small and contains 30 vCPU's and 60GB of RAM. Yes, I call that small as well ;)

<iframe width="560" height="315" src="https://www.youtube.com/embed/Y_Md_eFtVDQ?rel=0" frameborder="0" allowfullscreen> </iframe><br />

While we mainly position the SGI UV for scientific computing it is clearly a candidate for the cloud and telecommunications business as well. The ease of use in management and operations make it a solid choice over the current clustered approach of building highly scalable clouds. you can only go so far horizontally sometimes you need true supercomputers that can scale vertically. The [SGI UV](https://www.sgi.com/products/servers/uv/index.html) is a formidable platform for cloud computing.