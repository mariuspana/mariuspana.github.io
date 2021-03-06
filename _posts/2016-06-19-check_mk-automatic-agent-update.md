---
layout: post
title: "Check_MK 1.2.8 fully automatic agent updater for windows"
excerpt: "Check_MK 1.2.8 fully automatic agent updater for windows"
tags: [technology]
image:
  feature:
  credit:
  creditlink:
modified:
---


I last talked about [check_mk](http://mariuspana.github.io/check_mk-1.2.8/), version 1.2.8, and some of the new features available such as the fully automatic agent updater.

While everything works as expected on the Linux side there is an issue that may arise if you are using Microsoft Windows.

I ran across this while deploying the new automatic agent updater across some of our windows servers. The issue manifests itself thusly:

We install the new agent manually and proceed to register ourselves with the monitoring site.

![cmk-update-agent.exe](/images/Screen Shot 2016-06-19 at 12.56.26.png)

All would _seem_ to work well as there are no errors being reported.

![cmk-update-agent.exe success](/images/Screen Shot 2016-06-19 at 12.59.40.png)

However, when we looked in the MultiSite GUI we would see our windows server with a warning for the server we just set-up. The error looks something like this:

![cmk-update-agent.exe multisite failed](/images/Screen Shot 2016-06-19 at 13.01.23.png)

I am not completely sure if the following is the _proper_ way to resolve this issue but it works. Here is my justification for why I think this is so.

The issue stems from the fact that the check_mk_agent.exe runs under the "Local System" account and when we ran our registration step above we were doing it as a normal user (some may run this as an administrator). This would create the configuration files in the home directory of the user it is being run as instead of the "Local System" account.

I thought of running the check_mk_agent.exe service as a different user instead of the "Local System" account however I felt this would be more work than necessary especially since we have quite a few windows servers around.

To run the cmk-update-agent.exe as the "Local System" account download PSTools from [sysinternals][cd11d7ad] and use PSExec to run the cmk-update-agent.exe script as the "Local System" account.

  [cd11d7ad]: https://technet.microsoft.com/en-us/sysinternals/bb896649 "sysinternals pstools"


Running cmd.exe under the "Local System" account:

```
PsExec.exe -s -i cmd.exe
```

Now you can proceed as normal and run cmk-update-agent.exe as per the [documentation][d74affa2].

  [d74affa2]: https://translate.google.ro/translate?hl=en&sl=de&u=https://mathias-kettner.de/cms_agent_deployment.html&prev=search "Check_MK Automatic Agent Update"

Now there is one other way I managed to get by without using PsExec and that is by running the registration step as the normal user and looking in the root of your home directory (C:\Users\<user name\) and there you will find a file named cmk-update-agent.cfg. If your registration was successful then you could copy this file over to C:\Progra~\check_mk\config and all would be well. I have not looked at the ramifications of using this scenarios (does it persist, what happens when a new agent is installed, etc.).
