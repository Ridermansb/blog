---
layout: post
title: Como configurar multiplos usuários SSH para o mesmo host
tags: git
published: True
---

In `.ssh` folder, generate a new key:

{% highlight shell %}
$ ssh-keygen -t rsa -C "your-email-address" -f "id_rsa_OTHERUSER"
# Your identification has been saved in take_rsa.
Your public key has been saved in take_rsa.pub.
The key fingerprint is:
XXX
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
+-----------------+
{% endhighlight %}

Ensure ssh-agent is enabled:

{% highlight shell %}
$ eval "$(ssh-agent -s)"
# Agent pid 59566
{% endhighlight %}


Add your new key:

{% highlight shell %}
$ ssh-add ~/.ssh/id_rsa_OTHERUSER
# Identity added: id_rsa_OTHERUSER (id_rsa_OTHERUSER)
{% endhighlight %}

Edit your config file:

{% highlight shell %}
vim ~/.ssh/config
{% endhighlight %}

With this:

{% highlight shell %}
Host github-OTHERUSER
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_OTHERUSER
{% endhighlight %}


Your remote url should be:

{% highlight shell %}
git@github-OTHERUSER:company/myproject.git
{% endhighlight %}

You can update the user name and email in your repository

{% highlight shell %}
git config user.name "OTHER_USERNAME"
git config user.email "OTHER@EMAIL.COM"
{% endhighlight %}
