---
slug: rapid-prototyping-web-applications-with-ozone
date: 2016-03-20T16:47:38.577Z
title: "Rapid Prototyping Web Applications With Ozone"
template: "post"
draft: false
description: "The Ozone Widget Framework (also Ozone Platform or just plain Ozone) is, “A customizable open-source web application that assembles the tools you need to accomplish any task and enables those tools…"
category: ""
tags: [JavaScript,Design,Programming]
---

### Rapid Prototyping Web Applications With Ozone

[Posted on](https://aliasmrchips.com/2015/04/05/rapid-prototyping-web-applications-with-ozone/)

The [Ozone Widget Framework](http://www.ozoneplatform.org/ "Ozone Widget Framework") (also Ozone Platform or just plain Ozone) is, “A customizable open-source web application that assembles the tools you need to accomplish any task and enables those tools to communicate with each other.” There are several extremely useful features that Ozone provides out of the box.

First, authentication and authorization: you have to log in to use it and it provides a set of administrative tools to control access to dashboards and widgets. The authentication backend is retarget-able, so you can use any spring-security compatible approach. In particular, Ozone works well with [CAS](https://www.apereo.org/cas) for a comprehensive single sign on solution.

Second, layout: in order to truly leverage Ozone, you need to break up your application into widgets (i.e., application components). This allows you to dynamically arrange (and re-arrange) them into dashboards on the screen. Ozone includes an excellent dashboard designer with multiple layout options (dividers, tabs, accordions, etc.).

Third, inter-widget communication: if your application is broken up into pieces, you will need some mechanism to allow them to work together. Ozone accomplishes this three ways: pub/sub message queues, point-to-point remote procedure calls, and a “widget intents” api.

To get started, [download Ozone](https://www.owfgoss.org/nexus/content/groups/public/org/ozoneplatform/ozone-widgeting-framework/OWF-bundle/7.0.1/OWF-bundle-7.0.1-GA.zip). Be sure to grab the [help files](https://www.owfgoss.org/nexus/content/groups/public/org/ozoneplatform/ozone-widgeting-framework/OWF-help/7.0.1/OWF-help-7.0.1-GA.zip). The documentation is pretty comprehensive and well organized. Follow the directions there to get an instance up and running.

<figure>

![](/media/rapid-prototyping-web-applications-with-ozone-0.)

</figure>

Logging in (testAdmin1/password) will take you to the default dashboard, which includes a couple of widgets that demonstrate the pub/sub eventing library. For the purposes of this discussion, click on the dashboard switcher in the toolbar (second icon from the left):

<figure>

![](/media/rapid-prototyping-web-applications-with-ozone-1.)

</figure>

Switch to the “Contacts” dashboard (under “Investments”). This should bring up a map along with the “Contacts Manager” and “Directions” widgets.

<figure>

![](/media/rapid-prototyping-web-applications-with-ozone-2.)

</figure>

So, here we have an accordion layout on the left with two widgets and a tabbed area on the right (with a single tab). So, let’s say we just want to reverse the positions left and right. Normally, this would require recoding the layout and reloading the page (at a minimum). Here we can just edit the dashboard (or make a new one). With the dashboard switcher open, click the “manage” button and then edit the “Contacts” dashboard (the little pencil icon). Hit “OK” to keep the same name and go to the dashboard designer.

<figure>

![](/media/rapid-prototyping-web-applications-with-ozone-3.)

</figure>

Play around with it a bit… just drag elements from the palette on the right onto the canvas on the left. If things get out of hand, just hit “Reset” or “Cancel” and start over. For my dashboard, I am going to move the accordion to the right hand side and use the “Fit” layout for the left (where the map will go). Voila!

<figure>

![](/media/rapid-prototyping-web-applications-with-ozone-4.)

</figure>

At this point, you have a flexible framework for developing web applications that lets you easily try out multiple layouts with no further development effort. Once you arrive at a layout you like, it is a straight forward exercise to collect the widgets into a single view that can be deployed outside of Ozone (if that is your ultimate goal).
