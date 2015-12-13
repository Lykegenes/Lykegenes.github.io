---
title:  "Make a hashsum using command line"
date:   2015-12-13 15:00:00
description: Make a SHA 256 hashsum of a string in command line
---


This command works in OS X and Linux, simply replace the text.
{% highlight bash %}
echo -n "some_text" | shasum -a 256
{% endhighlight %}
