---
slug: a-practical-guide-to-the-celery-distributed-task-queue
date: 2016-03-20T17:06:15.894Z
title: "A Practical Guide to the Celery Distributed Task Queue"
template: "post"
draft: false
description: "Celery is an “asynchronous task queue/job queue based on distributed message passing”. The documentation is pretty good, and you should follow their guide to get started. However, the examples are…"
category: ""
tags: [Programming,Python,Distributed Systems]
---

### A Practical Guide to the Celery Distributed Task Queue

### Introduction

[Celery](http://www.celeryproject.org/) is an “asynchronous task queue/job queue based on distributed message passing”. The [documentation](http://docs.celeryproject.org/en/latest/index.html) is pretty good, and you should follow their guide to get started. However, the examples are pretty simple and it may not be obvious how to move beyond the basics. This guide demonstrates using Celery for a rudimentary “Map/Reduce” implementation we will call “Celery Soup”.

### Choosing a Broker

According to the documentation, [RabbitMQ](http://rabbitmq.com/) is the recommended message broker. In our experience, [Redis](http://redis.io/) has performed better, but your mileage may vary. Using the [Django ORM](http://djangoproject.com/) is an easy and effective way to get started using Celery… if you are already using Django. However, for this discussion, I will assume you have followed the “[First steps with Python](http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html)” guide from the Celery documentation.

### Writing a Task

A Celery task is simply a python function. From the documentation,

```
@app.task  
def add(x, y):  
  return x + y
```

There are a few pitfalls when writing tasks that may not be apparent from the simple example above. Primarily, you have to remember that the task can be executed anywhere a Celery worker is running and you can not count on any data not passed in as an argument to the task being resident on the worker node. The other important thing to remember is that tasks should be written such that it will not cause any problems if a task gets re-run.

To illustrate, let’s make a slightly less trivial function that downloads a file from a url (using the excellent [requests](http://docs.python-requests.org/en/latest/) library), parses it (with [BeautifulSoup](http://www.crummy.com/software/BeautifulSoup/)), and creates a word count histogram on the document text. This will be our “map” function:

```
@app.task  
def map(url):  
  c = Counter()  
  r = requests.get(url)  
  soup = BeautifulSoup(r.text)  
  for word in soup.get_text().split():  
    if word not in c:  
      c[word] = 0  
    c[word] += 1  
return c
```

For the reduce step, we will take a list of word count histograms and “reduce” them to a single dictionary. Here I am using the built-in [collections.Counter](https://docs.python.org/2/library/collections.html#collections.Counter) class to implement this functionality:

```
@app.task  
def reduce(counters):  
  res = counters[0]  
  for c in counters[1:]:  
    res += c  
  return res
```

Submitting Task(s)

To test our tasks, it is reasonable (and generally a good idea) to fire up a python shell and just call the function directly:

```
>>> soup.map(‘[http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report](http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report)')
```

Assuming the tasks above are defined in a module called ‘soup.py’. To submit the task to Celery, just add a ‘delay’:

```
>>> soup.map.delay(‘[http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report](http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report)')
```

Actually, don’t do that. Not yet, anyway. It will submit the task, but it will submit it to the default Celery queue, which we don’t necessarily want. In order for Celery to execute this task, it is necessary to start up a worker process. We can start a worker on the local node from the command line:

```
$ celery worker -A soup -Q soup -l info
```

I have used the -Q flag to tell the worker to only listen for and process tasks submitted to the ‘soup’ queue. This is especially useful when you are sharing a broker… I don’t want my worker to execute other people’s tasks and vice versa. But how do I get my results? Just assign the result to a variable and get the response. In this case, the “get” will block until the task is completed.

```
>>> r = soup.map.delay(‘[http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report](http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report)')  
>>> r.get()  
…
```

### Orchestration

So, now that our task is working, we can start processing documents. This amounts to submitting a bunch of tasks and then collating the results. For example, let’s find the top 25 words from the top 25 Wikipedia articles. From a shell:

```
>>> r = requests.get(‘[http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report](http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report)')  
>>> d = BeautifulSoup(r.text)  
>>> for table in d.find_all(‘table’, class_=’wikitable’):  
...   counters = []  
...   for link in table.find_all(‘a’):  
...     counters.append(soup.map(link.get('href')))  
...   m = soup.reduce(counters)  
...   for k in sorted(m, key=m.get, reverse=True)[:25]:  
...     print k, m[k]  
...  
the 24345  
of 14571  
and 12016  
to 9504  
in 8745  
a 8614  
^ 6710  
The 5937  
Retrieved 5009  
on 4542  
is 4254  
for 3933  
from 3662  
with 3374  
as 3297  
that 3049  
by 2879  
was 2643  
May 2044  
at 2010  
2015. 1952  
an 1891  
be 1876  
article 1797  
2014. 1672
```

Now that everything seems to be working, let’s convert this to use Celery (and bring it all together). To implement our data flow, we will use the “[chord](http://docs.celeryproject.org/en/latest/userguide/canvas.html#chords)” primitive. The syntax for using the chord (and any of the other “canvas” primitives) is a little bit different, but it is also the most general way of using Celery and is worth going over.

```
r = requests.get(‘[http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report](http://en.wikipedia.org/wiki/Wikipedia:Top_25_Report)')
```
```
d = BeautifulSoup(r.text)
```
```
for table in d.find_all(‘table’, class_=’wikitable’):
```
```
 # get a signature for the reduce callback  
  callback = reduce.s().set(queue=’soup’)
```
```
 # the header is a list of “map” signatures  
  header = [ map.s(‘[http://en.wikipedia.org](http://en.wikipedia.org/)'+link.get('href')).set(queue='soup') for link in table.find_all(‘a’) ]
```
```
 # the chord primitive is a barrier that waits for all of the tasks  
  # in the header to complete and provides the input to the callback
```
```
 res = chord(header)(callback)
```
```
 m = res.get()
```
```
 for k in sorted(m, key=m.get, reverse=True)[:25]:  
    print(k, m[k])
```

The task “signature” can be accessed using the “s()” function of a defined task. Here, we create a list of “map” signatures to submit and assign the “reduce” signature to a callback variable. These get submitted using the chord primitive and we get the response as before. Notice that using the task signature allows us to easily set the queue for the tasks as well.

This code can be cleaned up a bit by [routing tasks](http://docs.celeryproject.org/en/latest/userguide/routing.html), which is left as an exercise for the reader… or you can just check out the [code](https://github.com/aliasmrchips/celery-soup). Finally, while I can’t speak to the scalability of the “reduce” step, the “map” part scales quite nicely. In fact, we actually used celery to great effect [using almost 30,000 CPUs to process 1 petabyte of NASA imagery in just 16 hours](http://googlecloudplatform.blogspot.com/2015/05/Introducing-Preemptible-VMs-a-new-class-of-compute-available-at-70-off-standard-pricing.html) on the Google Cloud.
