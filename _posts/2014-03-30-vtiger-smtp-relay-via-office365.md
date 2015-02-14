---
layout: post
title: "(vTiger) SMTP relay via Office365"
excerpt: "How to get vTiger to relay emails via Office365"
tags: [vtiger, postfix]
<!--image:
  feature: sample-image-4.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/-->
---

Heres another in the [vTiger](http://www.vtiger.com) series. As mentioned previously at my [company](http://www.sphs.ro) we use vtiger CRM for our sales, marketing and helpdesk system. We also heavily use Microsoft's [Office365](http://www.office365.com) and needed a way to easily send emails from our CRM via Office365.

While you can technically set-up any email server anywhere to send from whatever domain you wish I wanted to benefit from better reputation that Microsoft's servers have and also having a single point of troubleshooting in case of email troubles.

In my case I have a centOS 5.x box running vtiger6 and postfix, hosted somewhere in the cloud. Here is what you will need to do to allow your own servers to to relay via office365:

* Log-in to you Office365 system and click on Admin and then select Exchange

![](/images/Screen-Shot-2014-03-30-at-13-17-57.png)

* Click on MailFlow and select connectors 
![](/images/Screen-Shot-2014-03-30-at-13-19-27.png)
![](/images/Screen-Shot-2014-03-30-at-13-19-57.png)

* Add an inbound connector. On the general tab give your connector a name and select on-premise. alt

![](/images/Screen-Shot-2014-03-30-at-13-20-51.png)

* On the security tab you may want to force the use of TLS and restrict based on IP. alt

![](/images/Screen-Shot-2014-03-30-at-13-21-21.png)

* Finally on the scope tab introduce the IP address(es) of your SMTP servers/devices and define your Associated accepted domains alt

![](/images/Screen-Shot-2014-03-30-at-13-22-31.png)

Note the (*) for the sender domains, I want to relay for all of our domains since we have multiple domains hosted with Office365.

You will need to make sure you are running postfix 2.11 or newer as there seems to be an issue with TLS in previous versions.

> smtp_sasl_security_options = noanonymous
> smtp_sasl_tls_security_options = noanonymous
> smtp_tls_security_level = encrypt 
> smtp_use_tls = yes

Next you need to identify the proper relayhost. Do not use smtp.office365.com. You must use your own MX. You can find the proper domain to use by cliking on Admin and selecting Office365 from your Office365 portal. 

![](/images/Screen-Shot-2014-03-30-at-13-10-00.png)

In the windows that opens identify the domain you want to relay mail for and click on the Active link right under the status heading. 

![](/images/Screen-Shot-2014-03-30-at-13-14-14.png)

Choose view DNS settings from the window that open and note the MX record. 

![](/images/Screen-Shot-2014-03-30-at-13-25-10.png)

Use the value you just noted to define the following in main.cf:

relayhost = [<domain-tld>.mail.protection.outlook.com]

After you restart postfix you should be good to go, all of you emails relaying via office365.

Make sure that you are sending from email accounts that exist otherwise your recipients will not be able to reply. Also take note that Microsoft does not want you to send spam or mass emails and [there are some limits](http://technet.microsoft.com/en-us/library/dn554323.aspx).