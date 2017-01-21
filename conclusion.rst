.. _conclusion:

Conclusion
==========

Possible Improvements
---------------------

Looking back on the work I did, there are two avenues I wish I would have had
the time to explore that I think would have improved our result.

The first had to do with the smoothing integer program. The goal of the model
was to minimize the variation amongst the days. Given that variation is a
quadratic function, I would have liked to have had the time to research
approaches to smoothing demand with an Integer Quadratic Program. If the
objective of our smoothing function could have been quadratic, I could have
been able to minimize the overall variation, resulting in a tighter smoothing
of all the days' demands.

I also would have liked to have cleaned up the routing integer program as
well. Constraints :math:`(8)-(9)` despite solving a key issue with the model,
were very computationally expensive as they grew exponentially relative to
the number of sites that had demand for a given day. Given how small the
problem size was for each day (never more than 10 locations), dealing with
exponential constraint growth was not an issue. However, finding a way to
model the problem in the same manner but without the exponential constraints
would be necessary for scaling the number of locations for which we could
solve at one time.

Closing Remarks
---------------

A brief bit about me. At time of writing, I'm a studying Industrial Engineer
at the University of Illinois. My interest areas lie in Operations Research
and Computer Science. I really enjoy Operations Research for the art of
modeling mathematically the world around me and having a means of making a
decision on what to do with whatever I have modeled. I developed an interest
in computer science out of my endeavors to make operations research more
valuable as computer science allows me to scale my models in size and wrap
them into programs that the average person can use. This work represents the
joining of both of my interests which I have been developing throughout my
education and serves as the capstone for my collegiate career. I could
not be more thankful for the people who have helped me get to this point,
especially my father who instilled in me the work ethic required to amass the
knowledge I needed to complete this project.
