.. Logistics Optimization for Regional Equipment documentation master file,
   created by sphinx-quickstart on Tue Jan  3 09:43:25 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Logistics Optimization for Regional Equipment
=============================================

This documentation explains the math, algorithms, and computer code used to
arrive at a solution for how to transport sets of construction equipment kept
in various geographical regions for a national construction company.

This problem concerns a construction company inquiring if it would be more
profitable to transfer the responsibility of delivering construction
equipment from the construction crews to an internal delivery service for
each geographical region in which they do business. Such a delivery service
would consist of semi-trucks and flatbed trailers, as well as equipment
haulers (truck drivers), and would be responsible for moving equipment to
construction sites in the region before jobs begin and from them once
they end. Currently, the construction crews have extra licensing and large
vehicles only fully utilized on days they move equipment to and from the
sites. A comparison of costs can be used to determine which is the more
effective system.

With the costs of the crews moving the equipment on their own known to the
construction company already, this work will focus on the calculation of
costs for the new delivery service, namely:

    * the number of semi-trucks and trailers
    * the number of equipment haulers (truck drivers)
    * the number of construction equipment sets

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

    * :ref:`reporting` outlines how all of the data the calculations from the
      previous two topics come together into one final report that can be
      used to make a decision on using the internal delivery system.

    * In :ref:`conclusion`, I'll give my closing remarks on the project.

I hope you enjoy reading, and thank you for taking the time to check out my
work!

Sean Kelley

Industrial Engineer, University of Illinois

Indices
-------

.. toctree::
   :maxdepth: 2

   main
   variation
   daily-routing
   reporting
   conclusion


