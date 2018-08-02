---
title:  "Resizing an APFS Container in a Sparsebundle"
date:   2018-08-02 12:30:00
description: Resizing an APFS Container in a Sparsebundle
---


First, make sure to unmount your Sparsebundle image.

We can now resize the file  :
{% highlight bash %}
hdiutil resize -size 2g ./my.sparsebundle
{% endhighlight %}

Now, mount it, and find out the identifier of the APFS Container you want to rewrite.

We can list all the mounted disks and partitions using :
{% highlight bash %}
hdiutil list
{% endhighlight %}

We can now resize the container! Here, we use `disk4s1` and a size of `0`, tu use all the available space in the Sparsebundle.
{% highlight bash %}
sudo diskutil apfs resizeContainer disk4s1 0
{% endhighlight %}
