---
slug: getting-started-with-jupyterlab
date: 2018-07-20T15:01:02.722Z
title: "Getting Started with JupyterLab"
template: "post"
draft: false
description: "Notebooks may not have completely replaced the scientific paper. However, they are increasingly used for exploration and “to create and share documents that contain live code, equations…"
category: ""
tags: [Data Science,Jupyter,Jupyter Notebook,User Experience,Geospatial]
---

### Getting Started with JupyterLab

Notebooks may not have completely replaced the [scientific paper](https://www.theatlantic.com/science/archive/2018/04/the-scientific-paper-is-obsolete/556676/). However, they are increasingly used for exploration and “to create and share documents that contain live code, equations, visualizations and narrative text” — according to [Project Jupyter](http://jupyter.org/). The Jupyter team took [your feedback](https://github.com/jupyter/surveys/tree/master/surveys/2015-12-notebook-ux) and tried to improve the user experience.

With JupyterLab now in [Beta](https://blog.jupyter.org/jupyterlab-is-ready-for-users-5a6f039b8906), it might be a good time to start looking at some of the differences with traditional Jupyter Notebooks and what it means for you. Follow the installation [directions](http://jupyterlab.readthedocs.io/en/stable/) first in order to follow along. Or, better yet, find a [JupyterHub](https://jupyterhub.readthedocs.io/en/stable/) installation near you and use that. However, if you really just want to “try before you buy”, check out the [demo binder](https://mybinder.org/v2/gh/jupyterlab/jupyterlab-demo/master?urlpath=lab/tree/demo) and follow along. This is what we will use for our examples.

<figure>

![](/media/getting-started-with-jupyterlab-0)

</figure>

JupyterLab still provides the classic Jupyter Notebook interface, but it integrates other useful features like a file browser, code console, terminal, and text editor; all as rearrangeable tabs in your browser to make it a more complete development environment. That’s pretty cool, but what really makes JupyterLab interesting are the extensions. For example, let’s say we have a simple JSON file…

```
{  
    "type": "FeatureCollection",  
    "features": [{  
        "type": "Feature",  
        "properties": {},  
        "geometry": {  
            "type": "Polygon",  
            "coordinates": [  
                [  
                    [-96.899414,  
                        39.046519  
                    ],  
                    [-104.12241,  
                        39.046519  
                    ],  
                    [-104.12241,  
                        35.496456  
                    ],  
                    [-96.899414,  
                        35.496456  
                    ],  
                    [-96.899414,  
                        39.046519  
                    ]  
                ]  
            ]  
        }  
    }]  
}
```

With a single click, JupyterLab will open the json in a new tab, pretty printed with rollup.

<figure>

![](/media/getting-started-with-jupyterlab-1)

</figure>

And because this example JSON happens to be GeoJSON, it can be previewed on a map in it’s own tab…

<figure>

![](/media/getting-started-with-jupyterlab-2)

</figure>

If this doesn’t work out of the box, you may need to install the GeoJSON JupyterLab [extension](https://github.com/jupyterlab/jupyter-renderers). While you’re there, checkout the vega2 and plotly extensions (among others). Another thing you may have noticed in the first screen shot is the Google Drive icon in the sidebar. This is also an [extension](https://github.com/jupyterlab/jupyterlab-google-drive), which demonstrates that JupyterLab can be extended in ways other than “just” handling different types of data. One of those datatypes worth calling out specifically is “Markdown” documents, which can be previewed in their own tabs. This makes it possible to edit the Markdown in one tab and see the rendered document in the other.

<figure>

![](/media/getting-started-with-jupyterlab-3)

</figure>

Anyway, what’s incredibly awesome is that the rendering extensions can be used directly in your Notebooks. For example, if I load the GeoJSON file above into a notebook, I can use the IPython.display module to display it in the output of the cell as JSON…

<figure>

![](/media/getting-started-with-jupyterlab-4)

</figure>

or GeoJSON…

<figure>

![](/media/getting-started-with-jupyterlab-5)

</figure>

Similarly, Jupyter Notebooks can handle Markdown input directly in the cells (select “Markdown” from the dropdown that says “Code”).

<figure>

![](/media/getting-started-with-jupyterlab-6)

</figure>

And lest you be worried, existing Notebook renderers will still work in JupyterLab. For example, matplotlib.pyplot still works fine

<figure>

![](/media/getting-started-with-jupyterlab-7)

</figure>

Another handy feature of JupyterLab is the integrated code console. Do you ever find your Jupyter notebooks littered with “scratch” cells used only for debugging and temporary work? Well now with a right-click, you can open a code console in a new tab, use it as a place for scratch work and keep the notebook clean and coherent.

<figure>

![](/media/getting-started-with-jupyterlab-8)

</figure>

If you like Jupyter Notebooks you’re sure to appreciate the more integrated experience in JupyterLab. And with it’s extensible design and focus on user experience, JupyterLab is sure to become the cornerstone tool for interactive and scientific computing.
