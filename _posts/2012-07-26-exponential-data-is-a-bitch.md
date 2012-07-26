---
layout: post
title: Exponential Data is a Bitch
---

### What is an Exponential Data Set?

You know you have [exponential growth](http://en.wikipedia.org/wiki/Exponential_growth)
in your data set when you plot out the number of rows in your database and you
get a hockey stick graph like the green line shown below.

<div class="row">
  <span class="span4 offset2">
    <img src="/img/exponential-data-is-a-bitch/graph.png">
  </span>
</div>

Exponential data is a double edged sword. On the one hand it is good to have
plenty of data to analyze. On the other hand you still need to be able to
manage that data. This is especially important when dealing with analytics
since while you may have exponential growth in your data set you also have to
deal with the exponential size of aggregation numbers.


### More Features, More Problems

A big part of understanding your data is actually getting in and playing with
it. That means drilling in and segmenting and splitting up your data to better
understand how it breaks down. However, each segment you create exponentially
increases the size of your aggregate results.

Let's say you have some clickstream data you want to analyze and you have 10
million page views spread across 100 pages on your site. If we want to
aggregate how many users went to each page then that's easy. We end up with
100 values in our aggregate result. Lets assume each number is an integer that
takes up 4 bytes. Our aggregate results take up 400 bytes now.

> 100 pages = 100 values (400 bytes)

But that's not very interesting. It doesn't tell us about who is on each page
or how they got there. So lets segment our data on gender. To find out how
many aggregate values we have we just multiple the number of possible values
in each feature together. There are two genders and we have one hundred pages:

> 100 pages * 2 genders = 200 values (800 bytes)

Next we'll segment by a feature with more possible values like a country.
There are 196 countries in the world (depending on who you ask) so our new
aggregate results are:

> 100 pages * 2 genders * 192 countries = 38,400 values (153,600 bytes)

That's quite a jump but it is still small data. We have now segmented the data
but we don't know where our users are coming from. Lets segment on the
previous page the user was coming from.

> 100 pages * 100 previous pages * 2 genders * 192 countries = 3.84MM values (15.4 MB)

If we want to go further back and see where the user was coming from in the
two previous pages it jumps again. 

> (100 pages ^ 3 steps) * 2 genders * 192 countries = 384MM values (1.5 GB)

We're at one and a half gigabytes now and we're getting some good segmentation
but we still only have a couple features. Lets do something much more
interesting now.


### Real World Scenario

Lets say you want to see a [Sankey diagram](http://en.wikipedia.org/wiki/Sankey_diagram)
like the [Google Analytics Flow Visualization](http://analytics.blogspot.com/2011/10/introducing-flow-visualization.html)
and you have to precompute your aggregates because you have 10 million page
views and your BI tool can't do real-time analysis on your data set. It would
be nice to be able to aggregate flow for an infinite number of steps but lets
limit it to 5 steps in our example. For our 100 pages without any feature
segmentation we get this:

> 100 pages ^ 5 steps = 10 billion values (40 GB)

40 gigabytes will take a while to compute and write to disk but once you have
the aggregates you can slice the data quickly. Now lets segment on several
features like gender (2 values), country (192 values), US state (50 values),
hour of the day (24 values) and day of the week (7 values).

> (100 pages ^ 5 steps) * 2 * 192 * 50 * 24 * 7 = 32 quadrillion values (128 Petabytes)

Now we're in trouble. Once we get into in-depth analysis and segmentation we
run into limitations on which features we can select to segment on when we
need to precompute our aggregate values.


### The Sledgehammer Algorithm

Most databases employ indexes to speed up the calculation of aggregate values
for a subset of the data but in our case we need to use all of our data. Also,
indexes suffer from the same exponential growth issue as our aggregate
results.

In the [Sky](https://github.com/skylandlabs/sky) database, we employ the
Sledgehammer Algorithm. I could show you a fancy mathematical formula but
here's the basic idea: _crunch through all your data really effing fast_. If
you can compute your aggregates fast enough across a large data set then you
don't need to precompute your aggregate results. The benchmarks for the latest
release of Sky show it computing aggregates at the rate of ~95MM events per
second on a single core. With linear distributed scalability that means you
can analyze over a billion events per second on a couple of commodity servers.


### Performance is a Feature

Typically when I tell people about Sky they wonder why it needs its own
query language or why it doesn't use a distributed file system like HDFS. In
the words of Fred Wilson, ["speed is the most important feature"](http://37signals.com/svn/posts/2280-speed-is-the-most-important-feature-if-your).

Sky is a domain specific database with a domain specific language. Whereas
other databases have to worry about transactional locking and MVCC, Sky is
storing log data which has already occurred and doesn't need transactions
across multiple inserts. Whereas other languages need to be general purpose,
Sky's language (called EQL) makes assumptions about how data is stored and
accessed in order to relentlessly optimize performance using stack allocation
and data locality.

The goal of Sky is to give you the ability to take an in-depth look at your
data in real time no matter what its scale. By making processing performance a
top priority, I believe a new wave of tools in fields like behavioral
analytics real-time predictive analytics are possible.

