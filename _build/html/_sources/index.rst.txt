.. Logistics Optimization for Regional Equipment documentation master file,
   created by sphinx-quickstart on Tue Jan  3 09:43:25 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the Documentation for Logistics Optimization for Regional Equipment!
===============================================================================

This documentation explains the formulation and implementation for solving
the Logistics Optimization for Regional Equipment problem. Before explaining
both of those, however, I would like to take a moment to give a background
on the problem itself.


This problem concerns a construction company inquiring if it would be more
profitable to transfer the responsibility of delivering construction
equipment from the construction crews to an internal delivery service.
Such a delivery service would consist of semi-trucks and flatbed trailers,
as well as equipment haulers (truck drivers), and would be responsible for
moving equipment to construction sites before jobs begin and from them once
they end. Currently, the construction crews have extra licensing and large
vehicles only fully utilized when moving equipment to and from the sites on
the days they begin and end. A comparison of costs will be used to
determine which is the more effective system.

With the costs of the crews moving the equipment on their own known to the
construction company already, this work will focus on the calculation of
costs for the new delivery service, namely:

    * the number of semi-trucks and trailers
    * the number of equipment haulers (truck drivers)
    * the number of additional equipment sets

Next, in :ref:`formulation`, I'll discuss the math models and algorithms I
developed to solve each part of the problem. Following that in
:ref:`implementation`, I'll discuss the code I wrote to execute each part of
the formulation.
        

Indices
=======

.. toctree::
   :maxdepth: 2

   formulation
   implementation

