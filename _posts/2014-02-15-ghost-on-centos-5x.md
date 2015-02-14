---
layout: post
title: "ghost blog on centos 5.x"
excerpt: "Example and code for using link posts."
tags: [ghost, blog]
---

As [previously](http://127.0.0.1:4000/welcome-to-idealistul/) mentioned I set this blog up on one of my existing centos 5.x servers and the installation guide provided by ghost.org does not cover systems running python versions prior to 2.7.

To the point: after you extract your ghost archive (or clone it from git, etc.) go ahead and set-up python 2.7. You can [follow this guide here ](http://elastic-computing.blogspot.ro/2012/08/install-python-27-on-centos-without.html)or download from [python.org](www.python.org) and place it somewhere on you system (I choose /opt/python2.7.6, for example).

All you need to do now is change you PATH variable so that you pick up the new python version instead of the one provided by your operating system. Here is how to achieve htis:

> export PATH=/opt/python2.7.6/bin/:/your/other/stuff/here

From this point you may continue as normal (npm install ....).