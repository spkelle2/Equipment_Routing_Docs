.. _formulation:

Problem Formulation
===================

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

To accurately model the cost of the internal delivery system, the size of the
problem had to be broken into smaller pieces, depending on how much could be
solved for at one time. I employed such a "divide and conquer" approach to
much of the formulation to help it run faster so longer ranges of time and
variations in geographical regions could be considered. Being able to
implement our formulation to multiple variations of the problem was paramount
to our confidence in our result.

Inputs
------

Regardless of which variation of the problem is run, there are a few inputs
to the problem that remain constant. They are as follows:

    * the start and end date of the range of time to be considered
    * the rate of travel of our semi-truck and trailer
    * the length of a working day for an equipment hauler
    * the amount of time on average it takes an equipment hauler to unload
      or load equipment from/on his trailer
    * the number of days (time window) in which a construction site is
      elgible for having equipment dropped-off or picked-up before/after
      its start/end date
    * the geographic regions for which our problem will solve
    * the different variations of each region (the differences being how
      many subregions of each region are to be considered)
    * the location of the file that has the data used by our problem. This
      file must include the following:

        * the geographical coordinates of each construction site worked on
          in our time range, separated by geographical region
        * the number of sets of construction equipment picked-up or dropped-
          off at each site each day, separated by variation of region

Region
------

For each different geographical region we test, we will find the cost of the
internal delivery system for each different variation of that region. The
best return on investment of all variations for each region will be the
variations used for comparing the costs of the internal delivery system to
the crews delivering their own equipment. Solving for any variation of any
region is formulated in the same way. A reporting tool will print the results
(number of semi-trucks, equipment haulers, and extra equipment sets) for
each variation of region, and the user may then calculate the respective
costs to determine which are optimal.

Variation of Region
-------------------

When calculating the cost of the internal delivery system for each variation
of region, it is important to find an optimal trade-off between the number of
semi-trucks and equipment haulers and the number of sets of construction
equipment needed. For example, prescribing that each construction site must
have its equipment dropped-off the day before work begins and picked-up the
day after it ends will result in a couple extremes. It will minimize the
number of sets of equipment needed to meet all construction sites' demand for
equipment (as it minimizes the amount of time equipment sets sit unused
waiting for pick-up) but will maximize the number of semi-trucks and
equipment haulers needed as some days may require many more equipment
movements than others. For that reason, the formulation requires a time
window (number of days before a construction site begins or after it ends) in
which a site must have its equipment sets dropped-off or picked-up. Adding
just a day to the time window (increasing it from 1 day to 2) will
dramatically decrease the number of semi-trucks and equipment haulers without
having much effect on the number of sets of equipment.

Assigning the time window as optimally as possible, the fist step for each
variation of region is to smooth the demand for the number of drop-offs and
pick-ups to be made each day as evenly as possible subject to all drop-offs
and pick-ups occuring within the time window. (The section "Smoothing Demand"
below will cover this process more in depth.) We then create two tables, the
first recording how many miles all semi-trucks run each day and the second
recording how many hours each equipment hauler works each day. We then return
to the data sheet with the number of equipment sets to be picked-up or
dropped-off at each site each day for this variation of region (which has now
been smoothed). For each day in that data sheet between our start and end
date, we make one list tracking which sites that day need a drop-off or a
pick-up and a second list tracking what the demand for drop-offs or pick-ups
at those sites are.

.. note::

    One drop-off or pick-up (1) is equivalent to one set of equipment (2) as
    that is the maximum capacity for a single truckload (3). These three
    terms will be used interchangeably moving forward.

    Furthermore, the number cooresponding to a construction site's demand for
    sets of equipment will be negative if the equipment is being dropped-off
    and positive if the equipment is being picked-up.

    Both of these pieces of information will be useful for interpreting the
    integer program responsible for figuring out how many semi-trucks and
    equipment haulers will be needed each day.


Now knowing which sites need drop-offs and pick-ups, as well as how many each
of those need, we can now formulate how many semi-trucks and equipment
haulers we need for each day. Before diving into that, though, let's take a
second to understand what's going on in the demand smoothing part I mentioned
above.

Smoothing Demand
----------------

As mentioned previously, the purpose of smoothing our demand for
equipment drop-offs and pick-ups is to greatly reduce the amount of
semi-trucks and equipment haulers needed to move all of the equipment while
slightly increasing the amount of equipment needed on hand.

We approached
this part of the problem by formulating an integer program. It is defined by
a set of job sites (indexed by :math:`i`), a set of days (indexed by
:math:`l`), and a set :math:`S` of sets of alternative days that
drop-offs/pick-ups can be made for a site :math:`i` with demand on day
:math:`l`. Each site :math:`i` has a demand, :math:`d_{i,l}`, stating how
many drop-offs or pick-ups it needs on day :math:`l`, with :math:`d_{i,l}` <
0 for drop-offs and :math:`d_{i,l}` > 0 for pick-ups. The sets within
:math:`S`, known as :math:`S_{i,l}` (indexed by :math:`l'`, as we are
indexing a subset of our set of days), are empty unless :math:`d_{i,l} \neq`
0. In that case :math:`S_{i,l}` is the set of the :math:`n` days before and
including the drop-off date or the :math:`n` days after and including the
pick-up date, where :math:`n` is the number of days in our time window. (If
there are fewer than :math:`n` days until the start or the end of our time
range, the set is just the remaining days.)

We used two variables within this problem definition. The first,
:math:`w_{i,l}`, represents the number of truckloads (equivalently, sets of
equipment) going to or from site :math:`i` on day :math:`l`. The second,
:math:`z`, is the largest sum of truckloads going to or from all sites on any
single day, :math:`l`. We can now form the following integer program:

.. math::

    &\text{minimize } &z & & & &(1)

    &\text{s.t.:} & & & &

    & \sum_{l' \in S_{i,l}} & w_{i,l'} & = |d_{i,l}| \text{ } & \forall
    & \text{ } i,l \mid S_{i,l} \neq \text{{}} &(2)

    & \sum_{i} & w_{i,l} & \leq z \text{ } & \forall & \text{ } l &(3)

:math:`(1)` tells us that we are minimizing :math:`z`, the maximum number of
pick-ups and drop-offs to be done on any single day. This is enforced by
:math:`(3)`, stating that our objective, :math:`z`, must be greater than or
equal to any single day's sum of drop-offs and pick-ups. Our last constraint,
:math:`(2)`, adds that for any site, :math:`i`, on any day, :math:`l`, with a
demand for drop-offs or pick-ups, the number of truckloads going to or from
that site within the time window must equal the number of truckloads
originally demanded to or from site :math:`i` on day :math:`l`.

Put in plain English, :math:`(2)` allows us to assign pick-ups and drop-offs
to different days as long as its sufficiently close to the original day they
needed done, and :math:`(1)` and :math:`(3)` ensure the number of trips being
made each day are as few as possible.




Individual Day
--------------



