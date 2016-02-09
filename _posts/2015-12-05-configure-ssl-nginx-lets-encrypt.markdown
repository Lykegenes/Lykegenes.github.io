---
title:  "Setup a free SSL certificate on Nginx with Let's Encrypt!"
date:   2015-12-05 14:30:00
description: SSL Certificate free nginx let's encrypt
---

[Let's Encrypt](https://letsencrypt.org) is a free certificate authorithy, but their certificates only last 90 days (for now) and do not support wildcard subdomains (again, for now).

So, we need an efficient (automated) way to register and renew our certificates. The [official Let's Encrypt client](https://github.com/letsencrypt/letsencrypt) is quite nice, but is not exactly automation-friendly. It's prompts a few questions, even in renewal mode. Also, it's virtual environment is a bit slow and eats to much memory for my 256MB VPS server.

I stumbled upon a side-project of one of the official client's main contributors : [simp_le](https://github.com/kuba/simp_le) It is an (almost) full-featured Let's Encrypt client, and mush more straightforward to use.

Clone it
{% highlight bash %}
cd /opt
git clone https://github.com/kuba/simp_le
cd simp_le
{% endhighlight %}

Install it
{% highlight bash %}
sudo ./bootstrap.sh
./venv.sh
ln -s $(pwd)/venv/bin/simp_le /usr/local/sbin/simp_le
{% endhighlight %}

and create a certificate
{% highlight bash %}
simp_le -d exemple.com:/var/www/website/root/ -f key.pem -f cert.pem -f fullchain.pem
{% endhighlight %}

To renew it, simply run the command again, adding one more parameter : `-f account_key.json`. 
You can also create a weekly Cron job. The certificate will only be replaced if necessary (close to it's expiration date).

Here are a few tools that can help us make sure our configuration is secure :

- [cipherli.st](https://cipherli.st) - They keep a collection of the latest, most secure SSL settings for most web servers.
- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/index.html) - A complete SSL certificate test of your domains. They even suggest recommendations of what to improve.
- [SSL Decoder](https://ssldecoder.org) - Another online SSL test utility
- [Certificate Expiry Monitor](https://certificatemonitor.org) - This project will scan your domain's certificate daily and warn you when it's about to expire
