---
layout: post
title: Introduction to Behavioral Databases
---

### Why Another Database?

Every database is built upon a trade off. Some databases excel at complex
relationships at the expense of speed while others may trade consistency for
availability. There's not a perfect database -- there's just the right database
for the right job.

Databases can also make a trade off by deciding to be general purpose or to
model specific data structures. A relational database lets you model anything
but because of that it doesn't know how to optimize storage of your specific
data or improve the querying of your particular data structures.

A behavioral database is built around the following premises:

1. Behavioral data is critical to business decisions.

1. There's a lot behavioral data to handle so it must be modeled efficiently.

1. Querying behavior is a specific use case so a specific language must be used.


### What is Behavioral Data?

Behavior is simply a collection of events by a single object over time. Each
event can be either an action or a change in state for the object.

For example, say you have the path of a user on your web site. First the user
lands on your home page (event `a`). Then they sign up for an account (event
`b`). Next 

<div class="asset">
  <a href="#"><img src="/img/introduction-to-behavioral-databases/simple_behavior.png"/></a>
</div>

