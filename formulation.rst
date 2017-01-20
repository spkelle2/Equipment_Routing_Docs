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

One issue I saw with the formulation we came up with is that if a couple of
days were to have a much higher demand for drop-offs and pick-ups than
all of the others, :math:`(3)` would not be a tightly enforced
constraint, and the other days' demands would not be smoothed effectively. To
work around this, we took a 'divide and conquer' approach to our final
formulation for demand smoothing. Rather than trying to smooth all of our
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


Individual Day
--------------

Knowing how many drop-offs and pick-ups needed made each day at this point,
we tried to route all equipment hauler movements throughout the time range in
one integer program. This proved quite unsuccessful, however, as our integer
program we modeled after that was so large it could not run in a feasible
amount of time. After some trial and error, we discovered that equipment
hauler routing (where each equipment hauler goes at each point in time) could
be done very efficiently when done one day at a time, thus everything in this
section is repeated for each day in our timeframe.

.. note::

    Due to the complexity of trying to route our equipment haulers, it was
    necessary to smooth the demand beforehand. Smoothing could also take
    place by routing for multiple days at a time, assigning time windows to
    deliveries in the same manner as in the smoothing process; however, doing
    this even for 2 or 3 days resulted in computation times above feasibility.

Before solving for the routes each equipment hauler will take each day, we
need to create a few parameters our routing integer program will need to be
able to solve. They are the following:

    * demand, :math:`d_{i}`
        The number of drop-offs and pick-ups each site needs on a given
        day, ignoring the sites with no demand.

    * locations (indexed by :math:`i`)
        A list of all sites with demand for drop-offs and pick-ups or,
        equivalently, the sites, :math:`i`, for which :math:`d_{i}` exists on
        a given day. This list also includes the hubs, where the equipment
        haulers start and end their day. They're represented as different
        numbers but can be same physical location. This list
        has length :math:`n+1`. The hubs are :math:`locations_{0}` and
        :math:`locations_{n+1}`.

    * customers (indexed by :math:`i`)
        A list of only just the sites with demand for drop-offs and pick-ups
        on a given day. Has length :math:`n-1`.

    * route constraints, :math:`D_{i,j}`
        A matrix of the number of times the route from site, :math:`i`, to
        site, :math:`j`, can be traveled, indexed in the same order as the
        locations parameter. Forces the model to adhere to real
        world constraints, like an equipment hauler not being able to drop
        equipment off at a site, :math:`j`, if he just came from a site,
        :math:`i`, where he also dropped equipment off. (Equipment always
        stays in a set and takes the full capacity of a semi-truck; therefore,
        this equipment hauler would have no equipment to drop off.) This will
        force an equipment hauler to return to the hub (where he starts and
        ends his day) to unload or reload his semi-truck when he can no
        longer meet the demand of any remaining sites with his semi-truck in
        its current state (either with an equipment set or empty).

    * travel, :math:`t_{i,j}`
        A matrix stating how many miles apart a site, :math:`i`, is from a
        site, :math:`j`, indexed in the same order as the locations list.
        The sites represented in this matrix are only those
        which have a demand for drop-offs or pick-ups on this day, and the
        distances are calculated by converting the differences in
        geographical coordinates listed for each site.

    * subsets, :math:`S_{m}` (indexed by i')
        A list of the even-sized subsets, :math:`m`, of sites with demand on a
        given day, necessary for forcing continuity in our equipment haulers'
        routes.

    * M
        An arbitrarily large number.

    * haulers (indexed by :math:`k`)
        A list of the equipment haulers we have available.

Also recall a few parameters we stated as inputs for each region we'll solve.

    * rate of travel, :math:`r`

    * length of working day, :math:`l`

    * average time to load/unload a semi-truck (handling time), :math:`h`

Lastly, the problem makes use of two variables. The first variable is
:math:`x_{i,j,k}`, the number of times equipment hauler, :math:`k`, takes the
route from a site, :math:`i`, to a site, :math:`j`. The other needed variable
is :math:`y_{m,k}`, whether or not an equipment hauler, :math:`k`, enters the
subset of points, :math:`m`.

Now that we have declared all of our parameters we will need to solve our
equipment hauler routing for each day, we can define our model.

.. math::

   \text{min} &\sum_{i} \sum_{j} \sum_{k} & &t_{i,j}x_{i,j,k} & & & & &(1)

   \text{s.t.:}

   &\sum_{j\backslash\{0\}} & &x_{0,j,k} & &\geq 1 & &\forall \text{ } k \in
   \text{haulers} &(2)

   &\sum_{i} x_{i,h,k} &-\sum_{j} &x_{h,j,k} & &= 0 & &\forall \text{ } h \in
   \text{customers}, k \in \text{haulers} &(3)

   &\sum_{i} & &x_{i,n+1,k} & &= 1 & &\forall \text{ } k \in \text{haulers}
   &(4)

   &\sum_{i} \sum_{j} & &x_{i,j,k}(h + \frac{t_{i,j}}{r}) & &\leq l + h &
   &\forall \text{ } k \in \text{haulers} &(5)

   &\sum_{j} \sum_{k} & &x_{i,j,k} & &= \mid d_{i} \mid & &\forall \text{ } i
   \in \text{customers} &(6)

   &\sum_{k} & &x_{i,j,k} & &\leq D_{i,j} & &\forall \text{ } i,j \in
   \text{locations} &(7)

   &\sum_{i' \in S_{m}} \sum_{j' \in S_{m}} & &x_{i,j,k} & &\leq My_{m,k} &
   &\forall \text{ } k \in \text{haulers}, m \in \text{subsets} &(8)

   &\sum_{i' \in S_{m}} \sum_{j \backslash S_{m}} & &x_{i,j,k} & &\geq
   y_{m,k} & &\forall \text{ } k \in \text{haulers}, m \in \text{subsets} &(9)

   & & &x_{i,j,k} & &\in Z \geq 0 & &\forall \text{ } i,j \in \text{locations},
   k \in \text{haulers} &(10)

   & & &y_{m,k} & &\in \{0,1\} & &\forall \text{ } k \in \text{haulers}, m
   \in \text{subsets} &(11)
 
:math:`(1)` tells us our objective is to minimize the total amount of time
that our fleet of equipment haulers spend driving each day. :math:`(2)-(4)`
add constraints for modeling the travel from one site to the next. :math:`(2)`
says that each hauler must leave the hub each day, even if just to return to it
at no cost (equivalent to not being used). If a hauler arrives at a site,
:math:`h`, to make a drop-off or a pick-up, :math:`(3)` ensures that hauler
also leaves that site for another. Once a hauler has made all drop-offs and
pick-ups it needed to make for a day, :math:`(4)` requires that he returns to
the hub. :math:`(5)` brings our time constraints into play. We required that
all driving and unloading/loading (un/loading) of semi-trucks must be less
than the length of the allowable work day. We then added the time of an extra
un/loading to the workable day to reflect that on average, a semi-truck will
not need un/loading at both the start and end of its day. :math:`(6)` covers
our demand constraint, the requirement that each site, :math:`(i)`, must be
visited as many times as it needs equipment dropped-off or picked-up each
day. To ensure that :math:`(6)` only counts the visits that correspond to the
type of demand the site has, :math:`(7)` applies the route constraints that
ensure when a hauler visits a site his semi-truck is able to satisfy one unit
of that site's demand. :math:`(8)-(9)` are additions to
:math:`(2)-(4)`, enforcing that equipment haulers take routes that are only
possible in the real world. :math:`(8)` monitors whether or not an equipment
hauler enters a given subset of sites, while :math:`(9)` requires that an
equipment hauler leave that set if he entered it. These constraints remove
the possibility that the routes a hauler takes in a day are disconnected.
Lastly, :math:`(10)-(11)` define the spaces for our variables, specifically
that equipment haulers can take no partial routes and either enter or do not
enter any given set of sites.

One final piece of information remains to fully define our integer program
for hauler routing, how many haulers (and therefore trucks) are available
to be used on a given day. Since the purpose of the problem is to use as few
assets as possible given a list of drop-offs and pick-ups to make each day,
we start by running the model with as few haulers as possible, one, and rerun
it, adding one hauler per rerun, until our model is feasible. Once we achieved
feasibility, we recorded the total number of miles the haulers drove that day
and how many hours each of the consequently worked into the tables created
before running the first day's models.


Reporting
---------

Text.















