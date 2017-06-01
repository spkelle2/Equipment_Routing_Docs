.. _daily-routing:

Daily Routing
=============

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

Creating Parameters
-------------------

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
        has length :math:`n+1`. The hubs are :math:`\text{locations}_{0}` and
        :math:`\text{locations}_{n+1}`.

    * customers (indexed by :math:`i`)
        A list of only just the sites with demand for drop-offs and pick-ups
        on a given day. This list has length :math:`n-1`.

    * route constraints, :math:`D_{i,j}`
        A matrix of the number of times the route from site, :math:`i`, to
        site, :math:`j`, can be traveled, indexed in the same order as the
        locations parameter. Forces the model to adhere to these real world
        constraints:

        * A hauler must alternate between dropping-off equipment and picking-
          up equipment.

        * A hauler can only use the demand from one site to satisfy an
          opposite demand at another as many times as is the minimum absolute
          value of demand between the two sites.

        This assignment is very particular. Please see the documentation below
        (make_route_constraints) and its definition in the parameters module
        for more details.
      
    * travel, :math:`t_{i,j}`
        A matrix stating how many miles apart a site, :math:`i`, is from a
        site, :math:`j`, indexed in the same order as the locations list.
        The sites represented in this matrix are only the hubs and those
        which have a demand for drop-offs or pick-ups on this day, and the
        distances are calculated by converting the differences in
        geographical coordinates listed for each site.

    * subsets, :math:`S_{m}` (indexed by i', a subset of a sites)
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

Developing the Model
--------------------

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

   &\sum_{i' \in S_{m}} \sum_{j' \in S_{m}} & &x_{i',j',k} & &\leq M(y_{m,k}) &
   &\forall \text{ } k \in \text{haulers}, m \in \text{subsets} &(8)

   &\sum_{i' \in S_{m}} \sum_{j \backslash S_{m}} & &x_{i',j,k} & &\geq
   y_{m,k} & &\forall \text{ } k \in \text{haulers}, m \in \text{subsets} &(9)

   & & &x_{i,j,k} & &\in Z \geq 0 & &\forall \text{ } i,j \in \text{locations},
   k \in \text{haulers} &(10)

   & & &y_{m,k} & &\in \{0,1\} & &\forall \text{ } k \in \text{haulers}, m
   \in \text{subsets} &(11)
 
:math:`(1)` tells us our objective is to minimize the total amount of distance
that our fleet of equipment haulers cover each day. :math:`(2)-(4)`
add constraints for modeling the travel from one site to the next. :math:`(2)`
says that each hauler must leave the hub each day, even if just to return to it
at no cost (equivalent to not being used). If a hauler arrives at a site,
:math:`h`, to make a drop-off or a pick-up, :math:`(3)` ensures that hauler
also leaves that site for another. Once a hauler has made all drop-offs and
pick-ups it needed to make for a day, :math:`(4)` requires that he returns to
the hub. :math:`(5)` brings our time constraints into play. We required that
all driving and unloading/loading (un/loading) of semi-trucks must be less
than the length of the allowable work day. :math:`(6)` covers our demand
constraint, the requirement that each site, :math:`i`, must be visited exactly
as many times as it needs equipment dropped-off or picked-up each
day. To ensure that :math:`(6)` only counts the visits that correspond to the
type of demand the site has, :math:`(7)` applies the route constraints that
force a hauler's visit to a site be one where his semi-truck is able to
satisfy one unit of that site's demand. :math:`(8)-(9)` are additions to
:math:`(2)-(4)`, enforcing that equipment haulers take routes that are only
possible in the real world. :math:`(8)` monitors whether or not an equipment
hauler enters a given subset of sites, while :math:`(9)` requires that an
equipment hauler leave that set if he entered it. These constraints remove
the possibility that the routes a hauler takes in a day are disconnected.
Lastly, :math:`(10)-(11)` define the spaces for our variables, specifically
that equipment haulers can take no partial routes and either enter or do not
enter any given set of sites.

Running the Model
-----------------

One final piece of information remains to fully define our integer program
for hauler routing, how many haulers (and therefore trucks) are available
to be used on a given day. Since the purpose of the problem is to use as few
assets as possible given a list of drop-offs and pick-ups to make each day,
we start by running the model with as few haulers as possible, one, and rerun
it, adding one hauler per rerun, until our model is feasible. Once we achieved
feasibility, we recorded in the tables created before running the first day's
models the total number of miles the haulers drove this given day and how
many hours each of them consequently worked.

Once this process has been repeated for each day given in our range of time,
we can then summarize the data we've recorded to understand the costs
associated with this variation's of region delivery system.

Continue on to :ref:`reporting`



Documentation
-------------
Daily routing wrapper function:

.. autofunction:: iterate.solve_day

Creating parameters wrapper function:

.. autofunction:: parameters.make_parameters

Demand:

.. autofunction:: parameters.make_demand_list

Route constraints:

.. autofunction:: parameters.make_route_constraints

Travel:

.. autofunction:: parameters.make_travel_matrix

Subsets:

.. autofunction:: parameters.make_subsets

Model implementation:

.. autofunction:: hauler_routing.route_fleet

Summary of fleet mileage accumulated on a given day:

.. autofunction:: recording.record_fleet_mileage

Summary of time equipment haulers spent working on a given day:

.. autofunction:: recording.record_hauler_hours



Indices
-------

.. toctree::
    :maxdepth: 1

    reporting

