---
layout: post
title: "nginx and the location block"
excerpt: "A lesson in nested location blocks"
tags: [business, technology]
image:
  feature:
  credit:
  creditlink:
modified:
---

I was recently performing a QA review of a couple of nginx servers and I kept running into the same configuration mistakes so I thought I'd throw a couple of words of wisdom about it here.

The configuration I was looking was a phpMyAdmin application so Ill use this as an example but first a little background.

Nginx logically divides configurations for different content into blocks. These blocks live in a hierarchical structure. The issue I was looking at was how the phpMyAdmin application was being secured with location blocks. Specifically after configuring phpMyAdmin you should restrict access to some sensitive directories such as setup/.

When using location blocks to restrict access make sure that if you start your location block using a regex your nested location blocks also use regex otherwise the nested location blocks will never match.

What I was seeing was something along the lines of this:

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_intercept_errors on;
        fastcgi_ignore_client_abort off;
    }

    location ~ ^/pma {
            allow 127.0.0.1;
            allow x.x.x.x;
            deny all;
            include fastcgi_params;
            include /opt/local/etc/nginx/mime.types;
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param APPLICATION_ENV development;
            fastcgi_index index.php;
            fastcgi_intercept_errors on;
            fastcgi_ignore_client_abort off;
    }

However anyone could still access /pma even if they were not in the allowed IP ACL. Digging a little deeper I came up with the following:

    location ~ \.php$ {
        location ~ ^/pma {
                allow 127.0.0.1;
                allow x.x.x.x;
                deny all;
                include fastcgi_params;
                include /opt/local/etc/nginx/mime.types;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param APPLICATION_ENV development;
                fastcgi_index index.php;
                fastcgi_intercept_errors on;
                fastcgi_ignore_client_abort off;
        }
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_intercept_errors on;
        fastcgi_ignore_client_abort off;
    }

Is this the best way (from a performance/security point of view) to achieve this?  Let me know what you think.
