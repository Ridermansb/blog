---
layout: post
title: Get Starter with Boxstarter
published: true
date: 2015-02-26
tags: cmd boxstarter
comments: True
---

**First import modules in admin powershell**
{% highlight PowerShell %}
Import-Module Boxstarter.Chocolatey
{% endhighlight %}


**Create a power shell script**
{% highlight PowerShell %}
Install-WindowsUpdate -acceptEula -getUpdatesFromMS
Set-WindowsExplorerOptions -EnableShowHiddenFilesFoldersDrives -EnableShowProtectedOSFiles -EnableShowFileExtensions -EnableShowFullPathInTitleBar
Set-TaskbarOptions -Size Small -Dock Bottom -Lock
{% endhighlight %}

**Allow running PowerShell scripts**
{% highlight PowerShell %}
Update-ExecutionPolicy Unrestricted
{% endhighlight %}

**Disable System Restore**
{% highlight PowerShell %}
disable-computerrestore -drive "C:\"
{% endhighlight %}

**Disable hibernate**
{% highlight PowerShell %}
powershell -Command "Start-Process 'powercfg.exe' -Verb runAs -ArgumentList '/h off'"
if (Test-PendingReboot) { Invoke-Reboot }
{% endhighlight %}

**Install chocolatey packages**
{% highlight PowerShell %}
choco install spotify
choco install dotnet3.5
choco install imdisk
if (Test-PendingReboot) { Invoke-Reboot }
{% endhighlight %}

Now yout can create a packge from script
{: .notice}

{% highlight PowerShell %}
New-PackageFromScript MyScript.ps1 "Dev-Boxstarter"
{% endhighlight %}

This will create a nuget package in local repository, see variable `$Boxstarter.LocalRepo`

**Package with contents**
You can create package with contents to copy later.
In this, I have in `my-files` folder, a `.gitconfig` file.

{% highlight PowerShell %}
New-BoxstarterPackage -Name "Dev-Boxstarter" -Description "Sample box" -Path "C\my-files"
{% endhighlight %}

Later, in your script you can usage this command to copy files:

{% highlight PowerShell %}
Copy-Item (Join-Path -Path (Get-PackageRoot($MyInvocation)) -ChildPath '.gitconfig') -Force $env:USERPROFILER\.gitconfig
{% endhighlight %}

**Customize scripts**
After execute `New-BoxstarterPackage`, a nuget package will be create in `$Boxstarter.LocalRepo` folder. Lets open `Dev-Boxstarter\tools\ChocolateyInstall.ps1`.

Lets add first script created in this new file.

**Generate package**
After finish configure scripts and files, we can make a nuget package:

{% highlight PowerShell %}
Invoke-BoxstarterBuild "Dev-Boxstarter"
$files = Join-Path -Path $pwd.Path -ChildPath "content"
New-BoxstarterPackage -Name "Dev-Boxstarter" -Description "A box for .net developers" -path $files
{% endhighlight %}

## Publish package

The first thing you mey now is that Boxstarte look for package in onrder: Local, [Chocolatey feed](http://chocolatey.org/packages), [Boxstarter Community Feed](https://www.myget.org/gallery/boxstarter) (hosted in [MyGet](http://myget.org/)), the first package with same name finded, will be use.

You can define sources like this:

{% highlight PowerShell %}
Set-BoxstarterConfig -LocalRepo "\\server\share\myRepo"
{% endhighlight %}

or

{% highlight PowerShell %}
Set-BoxstarterConfig -NugetSources "http://www.myget.org/F/MyNugetFeed/api/v2;http://chocolatey.org/api/v2;http://www.myget.org/F/boxstarter/api/v2"
{% endhighlight %}

### Myget for push package

We use a default community Boxstarter in MyGet to push our package.
First we need a accesstoken to push our package:

After sign up, get you access token in your profile on MyGet.

{% highlight Bash %}
nuget push SamplePackage.1.0.0.nupkg f1258b54-8143-4237-bc1a-89e14c3ab933 -Source https://www.myget.org/F/boxstarter/
{% endhighlight %}

## Install package
Boxstarter use a ClickOnce installation, this mens you can simple navigate a URl e all is good!

{% highlight PowerShell %}
START http://boxstarter.org/package/mwrock.DefaultInstall
ridermansb-devbox.boxstarte

$cred=Get-Credential
Install-BoxstarterPackage -PackageName "Dev-Boxstarter" -Credential $cred
{% endhighlight %}

# Sample

I create a [gist](https://gist.github.com/Ridermansb/284d4b628785ec5c00d1#file-readme-md) for my envairoments
