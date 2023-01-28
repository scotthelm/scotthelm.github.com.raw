---
layout: base
title: Coming Up For Air
show_title: true
tags: personal
---
I have spent the last 7 weeks underwater. Rather than exploring the depths of
some ocean floor, I was plumbing the depths of my knowledge and character.

## The Problem
We had just gotten a new client. They were excited about the technology that we
had to offer, and they wanted it. And they wanted it in time for their national
meeting.

What was proposed sounds simple - take some of their data and show it in a
 mobile site, but simple &ne; easy. We had a massive task ahead of us, and
simply not enough time to do it.

## The Solution: Heroic Effort
There was only one thing for it. I had to roll up my sleeves and make it happen.
My requirements were a UI mockup. (Which is not entirely a bad thing. Our UX
people are par excellence). From that and source data files I had to model the
application data, figure out how to get it full-text searchable, how to make
explicit the inferred security relationships, and turn the mockup into a living,
breathing amalgamation of css and javascript and html and ruby.

It was exhilarating. Designing the system with concurrency in mind, pushing
anything that would block execution out to a message queue and consuming it with
[Sidekiq](http://sidekiq.org). Ensuring that the proper index and types were
mapped in [Elasticsearch](http://elasticsearch.org), and then getting the models
transparently mapped into the index. Making sure there was enough indirection
built in so the system was flexible and resilient in the face of change.
Making sure my controllers were secure and light, my data retrieval fast and
efficient, all fully tested of course. It was intellectually stimulating.

However, there is a toll that late nights and intense focus take on the
unwary developer. I found myself starting to be short with my children, I
started to miss [Systema](http://www.charlotte-systema.com/) classes because
I was working. My relationship with my wife is and has been strong for lo these
17 years, but it takes tending and care to maintain.

In short, I was Ed Harris, and I had put on the liquid oxygen suit and had made
the deep dive. I cut the right wire. Now what?

## A Better Solution.

Heroic effort is only a good answer if you ignore the cost to the hero. Otherwise, it's not sustainable.
Sooner or later, you're going to run out of heroes. Prime developer talent is rare, so burning through it is
not an option. But avoiding heroic effort isn't that easy. 

The sales pressures on software development are real. To developers they seem
capricious or arbitrary. But there they are. And they often stem from a real
business need. Like having the software available before the national meeting.
Seems arbitrary. But. Having it at that time helps ensure adoption of the
 software. If I build something and nobody uses it, I have added zero value.

I think the real answer comes in having the business, sales, ops and development
all on the same page, communicating true costs and timelines to each other so
they can reach the best comprimise, and set the customer's expectations properly.

That puts the whole team on the submarine, working together to get to the surface.
It makes for a stronger, smarter team. One that learns to pull together in a crisis. One
that is less likely to write checks they can't cash. 

And, for the developer/development team, at least, it's a much nicer place to be than alone
at the bottom of the abyss.
