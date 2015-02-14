---
layout: post
title: "Don't Stop Using Nagios"
excerpt: "Here are my thoughts on why you should not stop using nagios. The answer is check_mk."
tags: [monitoring, nagios, check_mk]
image:
  feature: 
  credit: 
  creditlink: 
---
Have you seen some of these stop using nagios [talks](http://www.slideshare.net/superdupersheep/stop-using-nagios-so-it-can-die-peacefully), [videos](https://www.youtube.com/watch?v=Q9BagdHGopg) and [posts](http://blogs.gartner.com/jonah-kowall/2013/02/22/got-nagios-get-rid-of-it/)?

If you havent go ahead and look through them, Ill be here when you get back.

I believe nagios is a good and mature product for monitoring IT infrastructures. Nagios has [history](http://www.nagios.org/about/history), it is battle tested and proven to work. You may not like the configuration syntax, the UI or any other aspect of it but you can extend, customize and improve. This is something none of the speakers of the "stop using nagios" movement seem to be doing. They are just bitching and moaning.

There is however one project which didnt take the bitch and moan path and instead focused on building a better product. That project is called [OMD](http://omdistro.org/) (open monitoring distribution) and it is one hell of a fine product.

Had the "stop using nagios" movement fellas looked at OMD they may not complain so much.

Here is what the developers of OMD have to say:

> OMD avoids the tedious work of manually compiling and integrating Nagios addons while at the same time avoiding the problems of pre-packaged installations coming with your Linux distribution, which are most times outdated and provide no regular updates.

OMD bundles Nagios together with many important addons and can easily be installed on every major Linux distribution. We provide prebuilt packages for all enterprise Linux distributions and also for some other, such as Ubuntu.

And here are some of the features:

* multiple instances per host: OMD supports multiple but separated Nagios instances on the same machine - so called sites, i.e. for one production usage and one test environment.
* separate omd user per instance: each site has its own operating user. No root permissions are needed for administration.
* script based tarball building: if you decide to compile omd for yourself, you get a single tarball with all neccessary paths and binaries. Installing this tarball on hosts is as simple as extracting a common tar file.
* simple creation of new sites: omd create mysite create the site and omd start starts all stuff.
* supporting different omd version at the same time: you can install different versions in parallel, i.e. running your production instance with the last hard rock version and using the most current version in your test environment. There is an omd upgrade available, but at this time don't rely on it, it is experimental!
* plattform independent paths: omd installs to /opt/omd on all plattforms. If you don't like this, you can symlink omd to every location you want.
* speed optimizations: using ncpdmod (NEB module) for processing performance data, rrdcached to speed up rrdtools, tmpfs for check results and livestatus for some webfrontends we try to reduce the disk I/O.

As a major compoent of OMD the [Check_MK](http://www.sphs.ro/check_mk) project provides a completely new way of monitoring the IT infrastructure. One agent that does automatic inventory/discovery, an elegant interface and a platform that allows for all the above and much more. We may very well just now be getting to the point where we can actually use nagios to the fullest extent possible.

Sure, Ill give in a little, the configuration files were be a bit of a pain but the community responded with projects to handle [configuration management](http://www.nagios.org/projects/nagiosconfigtools). I know many a sysadmin that are just fine parsing status.dat and using NDO by the way. I also know many more people that are just as happy with the nagios configuration syntax as is; theyve been doing just fine for the past ohh 10 years or so.

I looked at and used Zabbix, sensuapp, open nms and in the past have used extensively HP OpenView/NNM and IBM Tivoli.

My general conclusions:

* 1. The naigos based ones are nothing more than nagios with some pretty (sometimes not so pretty) makeup (user interfaces)
* 2. the non-nagios guys seem to be trying to reinvent the proverbial wheel to bring their solutions into the web 2.0 ruby bling bling era (see sensu app)
* 3. They all suck, starting with the installation methods, continuing straight through with their configuration right down to mangement and operation. They are horribly broken, incoherent, seemengly pieced together pieces of code that form one amorphous blob of binary hell.

I need something functional, quick to set-up, easy to manage and operate. This turns out to be OMD with the nagios and/or check_mk micro core. Do whatever works for you but dont join the "stop using nagios" movement based on some rants. Study the market and you will quickly realise that nagios has got a lot going for it. It is much easier to expand upon rather than reinvent.

I am reminded of Henry Spencers quote which I think may apply at least in part to nagios.

"Those who don't understand Unix are condemned to reinvent it, poorly." – [Henry Spencer](http://en.wikipedia.org/wiki/Henry_Spencer)