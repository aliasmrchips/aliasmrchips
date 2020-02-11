---
slug: introduction-to-k8s-at-d11s-w-o-yaml
date: 2017-10-16T15:01:01.360Z
title: "Introduction to K8s at D11s (w/o yaml)"
template: "post"
draft: false
description: "At Descartes Labs we currently ingest about 25k images every day. By which I mean we download, recompress and store (in Google Cloud Storage) about 10TB of raw satellite data every day. This allows…"
category: ""
tags: [Cloud Computing,Kubernetes,Postgres,Django,Docker]
---

### Introduction to K8s at D11s (w/o yaml)

At Descartes Labs we currently ingest about 25k images every day. By which I mean we download, recompress and store (in Google Cloud Storage) about 10TB of raw satellite data every day. This allows us to provide a single, unified interface to many different datasets including:

*   Landsat (4, 5, 7, 8)
*   MODIS (Terra, Aqua)
*   Sentinel (1, 2, 3)

Our goal is to make this data available to you in the same way that we use it internally for [crop forecasting](https://www.descarteslabs.com/forecast.html), making [map composites](https://maps.descarteslabs.com) and [object detection](https://search.descarteslabs.com).

[**Count processed (23747790) from 08/09/2015 to 08/09/2017**  
_interactive graph and data of "Count processed (23747790) from 08/09/2015 to 08/09/2017…_plot.ly](https://plot.ly/~aliasmrchips/249.embed "https://plot.ly/~aliasmrchips/249.embed")[](https://plot.ly/~aliasmrchips/249.embed)

As of 2017–08–09 we have an archive of 23,747,790  images, representing 811.7 TB of compressed jp2 or approximately 8 PB of uncompressed data. Compression varies by data product and individual scene but we get about a factor of 10 on the average. This data is stored in Google Cloud Storage and accessed internally using our FUSE implementation where we have demonstrated: “…aggregate read bandwidth of 230 gigabytes per second using 512 Google Compute Engine (GCE) nodes” [https://arxiv.org/abs/1702.03935](https://arxiv.org/abs/1702.03935)
