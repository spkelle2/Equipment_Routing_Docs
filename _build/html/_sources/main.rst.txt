.. _main:

Main Function
=============

Overview
--------

This problem takes a historical approach to determining cost, using past data
on construction equipment usage to determine if an internal delivery system
would have been more cost effective than the construction crews moving their
own equipment. Our data file (outlined below) will tell us the number of sets
of construction equipment to be moved each day. This allows us to infer the
number of semi-trucks and equipment haulers needed to move all equipment each
day, as well how much equipment needed on hand to ensure each construction
site has their demand for equipment met. Whichever system would have been
more cost effective for the data we were provided will be chosen as the
system predicted to be more cost effective in the future.

Inputs
------

Regardless of which variation of the problem is run, there are a few inputs
to the problem that remain constant. They are as follows:

    * the start and end date of the range of time to be considered
    * the rate of travel of our semi-truck and trailer
    * the length of a working day for an equipment hauler
    * the amount of time on average it takes an equipment hauler to unload
      or load equipment from/on his trailer
    * upper bound on how many semi-trucks can be used on one day
    * the number of days (time window) in which a construction site is
      elgible for having equipment dropped-off or picked-up before/after
      its start/end date
    * the geographic regions for which our problem will solve
    * the different variations of each region (the differences being how
      many subregions of each region are to be considered)
    * the location of the excel spreadsheet that has the data used by our
      problem. This file must include the following:

        * the geographical coordinates of each construction site worked on
          in our time range, separated by geographical region. The first and
          last entries must have indices 9998 and 9999 and be the location of
          where haulers will start and end their days as well as where
          equipment will be kept. The first image below shows an example of
          what this should look like.    

        * the number of sets of construction equipment picked-up or dropped-
          off at each site each day, separated by variation of region. A
          negative number represents that many sets of equipment needing
          dropped-off and a positive one sets of equipment needing picked-up.
          The second image below shows an example.

.. image:: coordinates1.png

.. image:: demand1.png

Region
------

For each different geographical region we test, we will find the cost of the
internal delivery system for each different variation of that region. What is
meant by variation is the same geographical region, just contracted to include
fewer jobs or expanded to include more. The purpose for doing this is to help
us find variations where assets have high usage rates, which correspond to a
higher chance of being a success.

The best return on investment of all variations for each region will be the
variations used for comparing the costs of the internal delivery system to
the crews delivering their own equipment. Solving for any variation of any
region is formulated in the same way. A reporting tool will print the results
(number of semi-trucks, equipment haulers, and extra equipment sets) for
each variation of region, and the user may then calculate the respective
costs to determine which are optimal. However, since all of these results
will differ by variation of region, we'll move next to talking about what
parts of the code and calculation take place individually for each variation.

Continue to :ref:`variation`



Indices
-------

.. toctree::
   :maxdepth: 1

   variation


