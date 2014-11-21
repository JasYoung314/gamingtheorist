---
layout: post
title:  "A Simulating Challenge"
date:   2014-11-14 14:00:08
categories: Simulation Challenge Coding python
published: true
comments: true
---

#Learning to Code

My first introduction to coding was a bit of a whirlwind experience.

Two years ago (which seems so long ago at this point...) I was offered a summer internship working with [Dr. Vincent Knight](https://plus.google.com/+VincentKnight).
The aim of the project was to understand how people's (known as players) choices affecting a [particular type of queueing system](https://www.youtube.com/watch?v=OGZh2Py-iWk).
My part of this work was to build a simulation model capable of describing a system of \\( n \\)  \\( M/M/c \\) queues in series.
Before this I had no experience in programming (I have since learnt that Visual BASIC does not count).

I immensely enjoyed building the simulation model.
It was a puzzle, something to work at and outwit, trying to find the best possible way of handling the complexities of the system.
Even though I'll always love that first piece of real code I wrote, I must say it's a bit of a monster (and not in a good way).
I've since built a few different models with different aims, so I thought I'd share the basics of simulation and end with a *small* challenge.

# Simulation Basics

I'll now briefly run through how to simulate the most basic of queues : an \\( M/M/1\\)  

First thing to consider, when do I need to observe the system?
This will determine what sort of simulation you will be using.
For simulations that model fluids and other constantly changing systems you will need to continuously monitor the state of the system.
However much like queueing in real life, simulating a queue is short bursts of activity followed by a whole lot of nothing.
I don't need to constantly observe the system if nothing is happening.
Time is kept track of by the clock of a simulation model.
The clock keeps track of the current time and is used to determine what is happening in our system.
The only points in time that are considered are when an *event* happens.
This is known as a discrete event simulation model.
In a queueing system the events will be arrivals, as the state of the queue changes upon an arrival.
At this point we can generate a service time for the arriving player, place them in the queue and calculate the time at which they leave.
Another event would be the completion of a service, when a player leaves the system.
However, as the time that each player leaves the system is calculated upon arrival, no action needs to be taken and so they are not considered by the simulation model.

This process of simulating an arrival, proceeding to the next arrival, simulating an arrival etc. continues until a predetermined stopping condition is met, (usually a number of players have moved through the queue, or the required amount of simulation time has passed).


This process is described by the pseudo-code

{% highlight python %}

while stopping_condition == True:
    simulate arrival
    determine next arrival time
    move clock to next arrival 

{% endhighlight %}


Determining the next arrival time and advancing the clock is easy ( generate a random time based on the arrival rate into the system the set the clock to that time).
The area I've left out the detail for is "simulate arrival".

This process involves updating the state of the system and collecting all the data about that player (the time spent in the queue for example).
By keeping track of a few key numbers, simulating the simple queue is, well ... simple.
The only information that needs to be kept track of is the next time the queue is free.
This will be the first available time the player who just arrived can enter service.
Either that, or the player will arrive into and empty queue and immediately begin service.

So after a given player arrives, the next time the queue will be free is given by 


{% highlight python %} 

max(arrival_time, next_time_queue_is_empty) + players_service_time

{% endhighlight %}

With this information, it is easy to calculate information such as how long a player spends in the queue.

Which is the entire process for simulating the simple queue!

The idea is to build on this very simple idea. 
Adding multiple servers, multiple queues, different types of players and so on should all build on this simple simulation.
A book that I've recently acquired is [Stewart Robinson's](http://www.stewartrobinson.co.uk/) Simulation, which is a great place for anyone looking to take this further. 

# Challenge

So onto the challenge.
This is a simple problem, but how simple?

The challenge is to build a simulation model in as few lines of code as possible according to these three conditions

- The mean wait in the queue must be calculated correctly
- Must include a warm up period
- Must have minimum documentation explaining the code and any collaborators

The effort of [Vincent Knight](https://plus.google.com/+VincentKnight/posts), [Jason Young](https://plus.google.com/+JasonYoung/posts), [Geraint Palmer](https://plus.google.com/118222786508884333473/posts), [Harold Schilly](https://plus.google.com/+HaraldSchilly/posts), [Chris Rowlatt](https://plus.google.com/114311830974049426906/posts) can be found [here](https://github.com/drvinceknight/ShortMM1Q), which simulates the queue in just 9 lines! (including one line of documentation!)
Anyone who can beat this effort in the most elegant manner (judged by me) will be rewarded with a shiny "Inbox" invite. 
Leave a comment below with the link to a github repo with the solutions!

(Cheating by putting all the code on one line with semicolons will be disqualified, they're still multiple lines guys!)



 
