---
slug: geoserver-rendering-transformations
date: 2016-03-20T16:44:40.185Z
title: "GeoServer Rendering Transformations"
template: "post"
draft: false
description: "Given a large number of vector shapes, GeoServer already does a pretty good job of rendering them. In particular, GeoServer can easily render a vector layer that would cripple Google Earth. Believe…"
category: ""
tags: [CSS,JavaScript,GIS]
---

### GeoServer Rendering Transformations

Given a large number of vector shapes, [GeoServer](http://geoserver.org/) already does a pretty good job of rendering them. In particular, GeoServer can easily render a vector layer that would cripple Google Earth. Believe me, I’ve done it. It does this by rendering tiles (as images) and then delivering the tiles to the client application. Furthermore, if the tiles have been previously rendered, it can return a cached copy (using [GeoWebCache](http://geowebcache.org/)) for optimum performance. So, this is great, but sometimes, just being able to render the layer doesn’t convey any useful information. This is where [Rendering Transformations](http://docs.geoserver.org/latest/en/user/styling/sld-extensions/rendering-transform.html) may come in handy.

<figure>

![](/media/geoserver-rendering-transformations-0.)

</figure>

A heatmap representation of observations: an example of a Vector-to-Raster rendering transformation.

A regular Styled Layer Descriptor (SLD) is used to specify how a feature type or layer should be rendered: color, fill, line width, etc. Interestingly, this rendering process can be extended to process the data on request: Vector-to-Vector, Raster-to-Vector, or Vector-to-Raster. As an example of the last type, the heatmap style from the above example calculates the density of features according to the parameters passed to the heatmap function in the SLD. The resulting values are used to fill in a grid coverage (i.e., raster) and displayed according to a color ramp, also specified in the SLD. Note that in order for the layer to render correctly, you have to specify that you want a single tile (rather than a regular tiled layer).

<figure>

![](/media/geoserver-rendering-transformations-1.)

</figure>

The rendering transformation is re-applied for the current view… resulting in increased detail on zoom.

The heatmap style depends on being able to calculate a density. As a consequence, it becomes somewhat less useful once you have zoomed in enough to easily distinguish individual features. So, it makes sense to switch to a regular SLD at that point. While I appreciate the robust capabilities of SLDs, the XML schema can be somewhat daunting and prone to error. Fortunately, another incredibly awesome feature of GeoServer exists: [css styles](http://docs.geoserver.org/latest/en/user/extensions/css/index.html). This allows you to define styles in a much more developer friendly way. The css gets converted internally to SLD, so you can always tweak the XML if needed. For example, to display a grid of actual observations once you have zoomed in far enough, this is all that is required:

```
_[@scale < 10000000] {  
width: 1;  
stroke: ‘#b2df8a’;  
}_
```
<figure>

![](/media/geoserver-rendering-transformations-2.)

</figure>

The regular renderer turns on when it makes sense… also gives some indication of how the heatmap is calculated.

All of the GeoServer Rendering Transformations are built on top of the [web processing service plugin](http://docs.geoserver.org/stable/en/user/extensions/wps/install.html), so be sure to install that first. A great example of a Vector-to-Vector transformation is the [PointStacker](http://suite.opengeo.org/4.1/cartography/rt/pointstacker.html) style from the OpenGeo Suite documentation. Finally, while there seems to have been some [discussion](https://github.com/dwins/geoscript.scala/issues/21) around it, rendering transformations are not quite supported by the css style plugin as far as I can tell.
