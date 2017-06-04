.. _introduction:

Introduction
============

This documentation explains the math, algorithms, and computer code used to
arrive at a solution for how to transport sets of construction equipment kept
in various geographical regions for a national construction company. In the
general sense, the code explained details the necessary investments of
delivery vehicles and commodities to make feasible a single, large commodity
delivery network, where large means that one unit of the commodity equals the
capacity of our delivery vehicle. The single, large commodity delivery network
will be known throughout the rest of the documentation as "the internal
delivery system."

The current state of the construction company's delivery of equipment sets
(their large commodity) involves the crews working at each job site to take time
away from building at the start and end of their projects to move all of the
sets of equipment they need. Further adding to costs, the crews have extra
licensing and fuel inefficient vehichles they only fully utilize on the days
they move equipment. The construction company believed there to be a more
efficient means of operating.

In its inception, this problem concerned the construction
company inquiring if an internal delivery system using its own drivers for
moving sets of construction equipment would be more profitable than the crews
moving the sets of equipment on their own for each geographical region in
which they do business. Such a delivery system would consist of semi-trucks
and flatbed trailers, as well as equipment haulers (truck drivers), and would
be responsible for moving equipment sets to construction sites in the region
before jobs begin and from them once they end.

With the costs of the crews moving the equipment on their own known to the
construction company already, this work will focus on the calculation of
costs for the new delivery system, namely:

    * the number of semi-trucks and trailers
    * the number of equipment haulers (truck drivers)
    * the number of construction equipment sets

The output of the code described by this documentation is a report detailing
the utilization of semi-trucks/haulers and equipment sets. This report can
then be used to estimate the cost of the internal delivery system, leading us
to make a comparison of the two systems and a confidently made decision.

Python Implementation
---------------------

If you'd like to follow along in the code for which this documentation was
created, I invite you to check out its repository at
https://github.com/spkelle2/Equipment_Routing. Most following chapters have
a "Documentation" section detailing where specifically in the code you can
find what is explained here.

Topics
------

I'll cover a few different topics throughout this documentation in order to
describe the entire approach to determining if an internal delivery system is
a more viable option to construction crews transporting their own equipment.

    * :ref:`main` covers the inputs that the code requires to begin
      calculations. If you're looking for a full understanding of how the
      math, algorithms, and code interact, start here.

    * :ref:`variation` details the calculations that only need done once
      for each variation of a geographic region we consider. If you're only
      interested in the math, read this part and the next.

    * :ref:`daily-routing` explains the data manipulation and calculations
      that require being solved one day at a time, for each variation of
      region we consider.

    * :ref:`reporting` outlines how all of the data and calculations from the
      previous two topics come together into one final report that can be
      used to make a decision on using the internal delivery system.

    * In :ref:`conclusion`, I'll give my closing remarks on the project.

I hope you enjoy reading, and thank you for taking the time to check out my
work!

Sean Kelley

Industrial Engineer, University of Illinois



