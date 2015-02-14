---
layout: post
title: "Hearbleed Heaches"
excerpt: "Heartbleed bug creating headaches"
tags: [linux, openssl, security]
<!--image:
  feature: heartbleed.png
  credit: 
  creditlink: -->
---
![](/images/heartbleed.png)
<br />
Lots of talk about the [heartbleed bug](http://heartbleed.com/) these past two weeks and I guess it is all in [good measure](http://www.slate.com/articles/technology/bitwise/2014/04/heartbleed_security_flaw_it_s_bad_and_it_s_not_the_only_one.html?wpisrc=burger_bar). [Openssl](http://www.openssl.org/) is used by millions of us to secure our websites and now we know that private information may have been exposed.

So to cut is short you need to change your passwords. All of them. Make sure however that you change your passwords only if the sites you are using are no longer vulnerable to the heartbleed bug. But wait, it is not enough to just change your passwords. Your sites must also have changed their private keys as these may have been exposed. This is not going to be cheap for those operating hunders or thousands of servers.

While studying the potential damage that his bug has created, which is no easy task as there are no clues left behind, I relearned something about [perfect forward secrecy](http://en.wikipedia.org/wiki/Forward_secrecy). It would seem that had we been using PFS this may not have been such a huge deal.

So why are we not using PFS you ask? Because it is slower and we all know what waiting for page loading is like :(

Someone once told me that security is a trade-off between being comfortable and secure: the more secure your are, the more uncomfortable you are going to be.

Im pretty uncomfortable now and I expect things to get even more uncomfortable as we move along.