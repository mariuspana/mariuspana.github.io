---
layout: post
title: "vtiger6 and negative numbers"
modified:
excerpt: "A post about configuring vtiger so that it allows for negative numbers in invoices, credit notes, etc."
tags: [vtiger]
comments: true
---

I use [vtiger](http://www.vtiger.com) for our [company](http://www.sphs.ro) CRM. The software is good although lacking in the documentation and support department.  

One issue that my company encountered is the possiblity to provide credit notes and this requires the invoice to support negative values (this is due to Romanian accounting laws: we issue the entire envoice and then a storno with the negative amount). By default vtiger does not support negative numbers so we had to hack through un-documented code to ge to the root of the problem.  

For those that want a quick resolution here is where and what to modify so that you can use negative numbers.  

I assume you are in your vtiger6 document root. Edit the following file:  

`modules/Vtiger/resources/validator/FieldValidator.js`.  

At line 74 you should see the following:

>         /**
>          * Function to validate the Positive Numbers
>          * @return true if validation is successfull
>          * @return false if validation error occurs
>          */
>         validate: function(){
>                 var fieldValue = this.getFieldValue();
>                 var negativeRegex= /(^[-]+\d+)$/ ;
>                 /*if(isNaN(fieldValue) || fieldValue < 0 || fieldValue.match(negativeRegex)){
>                         var errorInfo = app.vtranslate('JS_ACCEPT_POSITIVE_NUMBER');
>                         this.setError(errorInfo);
>                         return false;
>                 }*/
>                 return true;
>         }

As you can notice I commented from line 82 to line 86 which is the entire if statement. That is the statement that verifies whether you are using a negative number.

I have not had the time to verify whther this breaks other functionality or not so use with caution. For our purposes so far it has not created any issues.

While on the subject of CRM you may want to have a look at [SuiteCRM](http://suitecrm.com/).
