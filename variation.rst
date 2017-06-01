.. _variation:

Region Variation
================

Importance of Time Windows
--------------------------

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

Set-Up for Daily Computations
-----------------------------

Assigning the time window as optimally as possible, the first step for each
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

Knowing which sites need drop-offs and pick-ups, as well as how many each
of those need, we can now formulate how many semi-trucks and equipment
haulers we need for each day. Before diving into that, though, let's take a
second to understand what's going on in the demand smoothing part I mentioned
above.

Smoothing Demand: Integer Program
---------------------------------

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

We used two variables in this problem. The first,
:math:`w_{i,l}`, represents the number of truckloads (equivalently, sets of
equipment) going to or from site :math:`i` on day :math:`l`. The second,
:math:`z`, is the largest sum of truckloads going to or from all sites on any
single day, :math:`l`. We can now form the following integer program:

.. math::

    &\text{minimize } &z & & & &(1)

    &\text{s.t.:} & & & &

    & \sum_{l' \in S_{i,l}} & w_{i,l'} & = |d_{i,l}| \text{ } & \forall
    & \text{ } i,l : S_{i,l} \neq \text{{}} &(2)

    & \sum_{i} & w_{i,l} & \leq z \text{ } & \forall & \text{ } l &(3)

    & & w_{i,l}, z & \in Z \geq 0 \text{ } & \forall & \text{ } i,l & (4)

:math:`(1)` tells us that we are minimizing :math:`z`, the maximum number of
pick-ups and drop-offs to be done on any single day. This is enforced by
:math:`(3)`, stating that our objective, :math:`z`, must be greater than or
equal to any single day's sum of drop-offs and pick-ups.
:math:`(2)` adds that for any site, :math:`i`, on any day, :math:`l`, with a
demand for drop-offs or pick-ups, the number of truckloads going to or from
that site within the time window must equal the number of truckloads
originally demanded to or from site :math:`i` on day :math:`l`. Our last
constraint, :math:`(4)`, ensures all our variables are non-negative integers.

Put in plain English, :math:`(2)` allows us to assign pick-ups and drop-offs
to different days as long as its sufficiently close to the original day they
needed done, and :math:`(1)` and :math:`(3)` ensure the number of trips being
made each day are as few as possible. :math:`(4)` simply prevents any half-
drop-offs or half-pick-ups from being made.

Smoothing Demand: Improvement Algorithm
---------------------------------------

One issue I saw with the formulation we came up with is that if a couple of
days were to have a much higher demand for drop-offs and pick-ups than
all of the others, :math:`(3)` would not be a tightly enforced
constraint on many of the other days, and their demands would not be smoothed
effectively. To work around this, we took a 'divide and conquer' approach to
our final formulation for demand smoothing. Rather than trying to smooth all of our
input days of data with the integer program in one go, we broke the data set
into equal sized chunks (periods) and had the integer program solve each
period individually.

To further optimize results, we repeated this process for periods with
lengths varying from a couple days to a couple weeks.

.. note::

    We picked the periods to be at least a couple of days so at minimum some
    of the day's demands in that period could be reassigned. We capped the
    periods' lengths at a couple weeks, however, because such large demands
    would appear for one day that our integer program would no longer smooth
    very effectively.

After running the integer program over all periods in our data for each
different period length, we picked the smoothed data we were going to work
with by whichever period length resulted in the least amount of variability
over all of its days (thus was the most smooth). Now that we have chosen the
smoothest data we can for drop-offs and pick-ups to be made each day, we can
formulate how we're going to figure out how many assets (semi-trucks,
equipment handlers, and equipment sets) we're going to need to meet each
construction site's demand.

With the number of drop-offs and pick-ups to be made to each site each day
now smoothed and a way to record our results created, we can move on to
calculating the routes that the fleet of haulers must make each day.

Continue to :ref:`daily-routing`



Documentation
-------------

Set-up for daily computation:

.. autofunction:: iterate.solve_variation

Creating the set, :math:`S`, and calling the smoothing integer program:

.. autofunction:: smoothing.iterate

Demand smoothing integer program:

.. autofunction:: smoothing.smoothing_model

Improvement algorithm/wrapper for demand smoothing functions:

.. autofunction:: smoothing.smooth_demand



Indices
-------

.. toctree::
   :maxdepth: 1

   daily-routing
