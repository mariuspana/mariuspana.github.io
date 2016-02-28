---
layout: post
title: "ansible and that darn cow"
excerpt: "cowsay not working for you? Here is something that may help."
tags: [technology]
image:
  feature:
  credit:
  creditlink:
modified:
---

At one point I changed from [brew](http://brew.sh/) to [pkgsrc](https://www.pkgsrc.org/) as my preferred package manager on OSX. It was at that time that I no longer had any awesome [cowsay](https://en.wikipedia.org/wiki/Cowsay) output while running my ansible playbooks.

Now that it's Sunday I finally had some time to go against the all-mythical's will and get some work done >:}

It seems that ansible does not check for the cowsay program to exist except for in a [particular set of locations](https://github.com/ansible/ansible/blob/dc6f0c12900d532aa78ca876fcd1f1269f02c2ff/lib/ansible/utils/display.py). pkgsrc installs things into /opt/pkgsrc/bin/<name> which is not a place where ansible checks for cowsay. Id change this to check for the binary cowsay anywhere within PATH but maybe they've got their reasons for doing it this way.

The solution as can be expected is to create a symlink for cowsay in /usr/bin or /usr/local/bin and you'll get back that awesome cow.

    __________________
    < Im back bitches! >
    ------------------
           \   ^__^
            \  (oo)\_______
               (__)\       )\/\
                   ||----w |
                   ||     ||
