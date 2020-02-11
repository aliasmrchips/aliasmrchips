---
slug: who-needs-backups
date: 2016-03-20T17:08:34.163Z
title: "Who needs backups?"
template: "post"
draft: false
description: "A couple of months ago I spilled a glass of water on my laptop. For the few days my laptop spent covered in rice in a cooler, I was able to continue working without disruption on a ChromeBook from…"
category: ""
tags: [Nodejs,Programming,Cloud Computing]
---

### Who needs backups?

A couple of months ago I spilled a glass of water on my laptop. For the few days my laptop spent covered in rice in a cooler, I was able to continue working without disruption on a ChromeBook from the office. I would like to say this was made possible because I was able to restore all my files from Backblaze or some such, but I actually don’t have backups setup on my laptop.

<figure>

![](/media/who-needs-backups-0.)

</figure>

You see, for the past six months or so, I have been developing almost exclusively on Virtual Machines in “The Cloud”. Specifically, I do all source code modification and software testing on remote servers. True, I have a few applications on my laptop that I use for some specific tasks: previewing images, database management, spreadsheets, etc. But for the most part, the only application I need is a browser.

<figure>

![](/media/who-needs-backups-1.)

</figure>

This is made possible largely by using [Cloud9](https://c9.io/). Cloud9 is a great service on it’s own and highly recommended if you do not manage your own infrastructure. However, if you are fortunate enough to be able to provision your own VMs, the [Cloud9 SDK](https://github.com/c9/core) is what you want.

Cloud9 is a node based server application and (not surprisingly) a node installation is required In order to run it. This will need to be installed if you do not have it already. For example, on Ubuntu:

```
# curl -sL [https://deb.nodesource.com/setup](https://deb.nodesource.com/setup) | sudo bash -  
# sudo apt-get install nodejs -y
```

Follow the instructions on [github](https://github.com/c9/core) to check out and run the IDE. If you are running on a remote server, be sure to specify the address to listen on (so you can get to it) and to turn on basic auth (because, security). For example, assuming you cloned into /opt/core, as a regular user run:

```
# node /opt/core/server.js -l 0.0.0.0 -p 8181 -w /opt/www --auth username:password
```

In addition to being a full featured IDE with syntax highlighting (for many languages), code completion, and many other bells and whistles, Cloud9 gives you command line access with full privileges for the user running the server process (which is awesome, but another good reason not to run it as root).

One of the great things about this is that shell sessions do not get disrupted if you close the browser, put your computer to sleep or even shut down (provided that the server process is still running). I would recommend running the server process from a screen session or creating a supervisor task to prevent this from happening. At any rate, you can even leave a command running and come back later, possibly from a different computer, and pick up right where you left off.

<figure>

![](/media/who-needs-backups-2.)

</figure>

But, Wait! There’s More! In collaboration mode (using the –collab flag at startup) you can share the workspace with others. The IDE includes a group chat, terminal sharing, and real-time updates to editor windows. That is, you can watch each other type. Combined with in-application file history (you can replay changes, create checkpoints and revert to arbitrary versions), Cloud9 makes for an excellent tool for distributed teams as well.

<figure>

![](/media/who-needs-backups-3.)

</figure>

Recently, I actually had a need to do some local development and, since I had gotten so used to the Cloud9 IDE, I wound up installing it locally. My conversion is complete! Oh, and my laptop is just fine.
