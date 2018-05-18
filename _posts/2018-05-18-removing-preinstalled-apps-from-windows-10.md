---
title:  "Removing pre-installed apps from Windows 10"
date:   2018-05-18 11:15:00
description: Removing pre-installed apps Windows 10
---

Since Windows 10 now installs many shitty apps and games, we must find a way to remmove the easily. I tried uninstalling them from the Control Panel, but they always seem to be coming back.

Fortunately, there is a solution!


First, open PowerShell in Administrator mode. Then run the following commands, depending on you needs :

To list all the apps installed for the current user :
{% highlight powershell %}
Get-AppxPackage | ft Name, PackageFullName -AutoSize
{% endhighlight %}


To list all the apps installed for all the users :
{% highlight powershell %}
Get-AppxPackage -AllUsers | ft Name, PackageFullName -AutoSize
{% endhighlight %}


To remove a specific app using a wildcard (*) :
{% highlight powershell %}
Get-AppxPackage *xboxapp* | Remove-AppxPackage
{% endhighlight %}


To remove all the pre-installed apps for all the users :
{% highlight powershell %}
Get-AppxPackage -AllUsers | Remove-AppxPackage
{% endhighlight %}


To prevent to apps to be installed for new users :
{% highlight powershell %}
Get-AppXProvisionedPackage -online | Remove-AppxProvisionedPackage –online
{% endhighlight %}
