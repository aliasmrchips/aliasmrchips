---
slug: secure-web-services-using-postgres-and-wfs
date: 2016-07-26T17:10:26.864Z
title: "Secure Web Services using Postgres and WFS"
template: "post"
draft: false
description: "Let’s say you have a bunch of data in a postgres database and you want to be able to access that data through a secure web service endpoint… and you don’t really want to write any code to do it…"
category: ""
tags: [JavaScript,Postgres,Web Services,Json]
---

### Secure Web Services using Postgres and WFS

Let’s say you have a bunch of data in a [postgres](http://www.postgres.org) database and you want to be able to access that data through a secure web service endpoint… and you don’t really want to write any code to do it. Maybe you are trying to just prototype an application; maybe you are not exactly sure what data you need; or maybe you just want to flex your SQL muscles. Whatever your reason, here’s how you do it.

<figure>

![](/media/secure-web-services-using-postgres-and-wfs-0.jpeg)

<figcaption><a href="http://www.inawonderworld.com/2013/08/01/you-can-do-it-revamping-a-classic/" class="figcaption-link">You can do it — revamping a classic</a></figcaption></figure>

We already know that GeoServer is [pretty great](https://medium.com/@aliasmrchips/geoserver-rendering-transformations-4d515b73904e#.y0wpt6uut) at rendering large vector layers using [WMS](http://www.opengeospatial.org/standards/wms). Of course, this assumes that you don’t want to use [WFS](http://www.opengeospatial.org/standards/wfs) to get at the actual data. While the situation is changing rapidly with the advent of [vector tiles](https://2016.foss4g-na.org/session/vector-tiles-geoserver-and-openlayers), as well as “compressed” vector formats like [TopoJSON](https://github.com/mbostock/topojson); historically it has been a very bad idea to try and render large numbers of vector shapes on the client (i.e., in the browser). The main reason being that it is very expensive to package and deliver all those pesky geometries; not to mention the risk of crashing your browser trying to render them. However, if we get rid of the shapes and only deliver the attributes… we are back in business.

<figure>

![](/media/secure-web-services-using-postgres-and-wfs-1.png)

<figcaption>TopoJSON example: <a href="http://bl.ocks.org/mbostock/4206573" class="figcaption-link">Area Choropleth</a></figcaption></figure>

Naturally, this approach won’t work very well for displaying a map (unless we can separate the data from the geometries and rejoin them later, which is a whole other story). However, it is surprisingly effective for delivering data; in particular if you use [SQL views](http://docs.geoserver.org/stable/en/user/data/database/sqlview.html). If you are not familiar with SQL views in GeoServer, go learn about them and then come back here.

<figure>

![](/media/secure-web-services-using-postgres-and-wfs-2.gif)

</figure>

Back already? Cool. As an example, here is a query to get only the Great Lakes from the [Natural Earth 10m physical vectors](http://www.naturalearthdata.com/downloads/10m-physical-vectors/) data set (assuming you have already imported the shapefile to postgres/postgis):

```
SELECT * FROM ne_10m_lakes WHERE name_alt = 'Great Lakes'
```

I should point out that the SQL can be arbitrarily complex: with [common table expressions](http://www.craigkerstiens.com/2013/11/18/best-postgres-feature-youre-not-using/), [pivot tables](http://imasdemase.com/en/programacion-2/tablas-cruzadas-en-postgresql-pivotmytable/), etc. The trick now is to restrict the properties returned using the [propertyName](http://docs.geoserver.org/latest/en/user/services/wfs/reference.html) key in the WFS request to explicitly exclude the geometry field:

```
http://example.com/geoserver/wfs?  
  service=wfs&  
  version=2.0.0&  
  request=GetFeature&  
  typeNames=ne_10m_lakes&  
  maxFeatures=50&  
  outputFormat=application/json&  
  propertyName=gid,featurecla,scalerank,name,name_abb,name_alt
```

You can hard code the properties if you know what you want or make it dynamic, in multiple steps, by first making a DescribeFeatureType request:

```
http://example.com/geoserver/wfs?  
  service=wfs&  
  version=2.0.0&  
  request=DescribeFeatureType&  
  typeNames=ne_10m_lakes
```

Alternatively, you can (somewhat perversely) create a layer that does not include a geometry at all, in which case you do not need to exclude the geometry field. However, note that you will have to manually define a projection for the layer in GeoServer (I use EPSG:4326, but it really doesn’t matter).

```
SELECT gid, featurecla, scalerank, name, name_abb, name_alt FROM ne_10m_lakes WHERE name_alt = 'Great Lakes'
```

In any case, what gets returned is (technically) a GeoJSON FeatureCollection. Practically speaking, it is a JSON object with a ‘features’ element that is a list of objects with a properties dictionary in the ‘properties’ element of each object.

```
{  
    "type": "FeatureCollection",  
    "totalFeatures": 10,  
    "features": [{  
        "type": "Feature",  
        "id": "great_lakes.fid--19b9acdb_155eb25be64_409",  
        "geometry": null,  
        "properties": {  
            "gid": 12,  
            "featurecla": "Lake",  
            "scalerank": 0,  
            "name": "Lake St. Clair",  
            "name_abb": "L. St. Clair",  
            "name_alt": "Great Lakes"  
        }  
    },  
    ...  
    }]  
}
```

From here, you can do whatever you want with the JSON: make a table, a chart, join the data back to the map to make a [choropleth](https://en.wikipedia.org/wiki/Choropleth_map), etc.

Oh yes, if you have set up GeoServer [security](http://docs.geoserver.org/stable/en/user/security/) (which you should) and have GeoServer running over SSL (which you should), your new “web service” is now secure as well. Furthermore, if you parameterize the view, the web service is programmable using the viewparams key (an exercise left to the reader).
