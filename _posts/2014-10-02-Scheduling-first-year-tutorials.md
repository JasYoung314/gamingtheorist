---
layout: post
title:  "Scheduling First Year Tutors"
date:   2014-10-02 14:00:08
categories: Scheduling Tutorials Eductaion
published: true 
comments: true
---

This year I've been asked to be a teaching assitant for [Dr Vincent Knight's](https://plus.google.com/+VincentKnight/posts) Computing for Mathematics [module](http://vincent-knight.com/Computing_for_mathematics/).
There's a variety of issues I've been asked to take charge of but the first was scheduling which tutors will be taking tutorials.

My initial reaction was to sit down and work it out with a pen and paper.
This wouldn't take very long, after all the availability of certain tutors would mean that certain tutors would have to go in certain slots, and the schedule would just fall into place.
Once it was done it would be done, right?

Not quite, there were a couple of problems with this

1. Availabilty can change, this would mean completely redoing the timetable.
2. I'm and idiot and can make mistakes

So I decided to write a piece of sage code (available [here](https://github.com/JasYoung314/scheduler) ) , let's look at the mathematics behind the code.
I was able to solve the timetabling problem with some basic optimisation technique's.

In any optimisation problem, the first thing to do is to think about what variables you will need.
The variables will conatin all the aspects of the problem.

- \\(T\\) is the set of all the available tutors. 
- They are then broken down into two groups, one group with are doing two hours a week \\(T_1\\), and the other group which are doing six hours \\(T_2\\).
- The set \\(S\\) is the set of all the tutor slots. I've used the actual names instead of time slots (Although at this point that information is seared into my brain).
- These are again broken down into sets \\(S_k\\). I'll explain the logic behind this later, but \\(S_k\\) is the set of all tutorials taking place at time \\(k\\).
- \\(a\_{ij}\\) dentoes the availability of a tutor \\(i\\) to take tutorial \\(j\\) (Due to the changing schedules and timetables of the university, these variables were cutting edge research). These were collected and fed in as a matrix of ones and zeros.
- Finally we have the decision variables \\(x\_{ij}\\).
These are the only values that we can can change. All the other numbers are fixed values based on the real world constraints (like the availability).
\\(x\_{ij}\\)  is a binary variable (only takes the value zero or one) and denotes that tutor \\(i\\) takes tutorial \\(j\\). In other words the \\(x\_{ij}\\) are the schedule

Scheduling is a well understood problem in mathematics, and can be formulated as a simple math program.

\\[ \max \sum\_{i\in T \; j \in S} x\_{ij} \\]


subject to

\\[   \forall \;i \in T\_1 \sum\_{j \in S} a\_{ij}x\_{ij} = 2 \\] 
\\[   \forall \;i \in T\_2 \sum\_{j \in S} a\_{ij}x\_{ij} = 6 \\] 
\\[   \forall \;j \in S \sum\_{i \in T} x\_{ij} = 2 \\]
\\[   \forall \;i \in T \; \forall k \in K \sum\_{j \in S\_{k}} x\_{ij} \leq 1 \\]
\\[   x\_{ij} \in \\{0,1\\} \\]
\\[   a\_{ij} \in \\{1,m\\} \\]


We want to maximise the objective function. This will fill as many tutorials as possible (All of them ideally) subject to the following constraints.
The first two constraints are doing the same thing but for different types of tutors.
They ensure that tutors are doing the number of hours that they should be.
By adding \\(a\_{ij}\\) to the sumation, it is ensured that all the tutors are available.\\(a\_{ij}\\) is 1 if the tutor is available or \\(m\\) if they are not. This value \\(m\\) needs to be sufficiently large to break the constraints if the algorithm tries to assign a tutor to a slot where they are not available (in my code \\(m=20\\))  
The third constraint makes sure that there are two tutors in each tutorial. Nice and simple.

The final constraint ensures that the tutor is not meant to be in two places at once. 
The time slots are broken up into sets which contain all the tutorials which occour at the same time, and by making sure that one tutor only takes at most one tutorial from each of those sets, this makes sure that nobody gets double booked.

Sage has some nice ways of coding this up, aswell as a function which simply optimises the math program. So once I input the matrix of availability I was done right?

*No Plan Survives Contact With The Enemy*   



