.. _conclusion:

Conclusion
==========

Possible Improvements
---------------------

Looking back on the work I did, there are a couple avenues I wish I would
have had the time to explore that I think would have improved our result.

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

Lastly, I think the reusability of the project could have benefitted from
having a more automated way of generating data for the model to use. Due to
time constraints, the best solution we could come up with was pulling data
from a database and moving it by hand into separate spreadsheets, but I
think that could have been accomplished with much less difficulty on the end
user if an end-point for a database and a query were input instead. That way,
the data on locations and demand for drop-offs and pick-ups for each
variation could have just been mined automatically, saving time and stress on
the user.

Biography
---------

A brief bit about me. At time of writing, I'm a studying Industrial Engineer
at the University of Illinois. My interest areas lie in Operations Research
and Computer Science. I really enjoy Operations Research for the art of
modeling mathematically the world around me and having a means of making a
decision on what to do with whatever I have modeled. I developed an interest
in computer science out of my endeavors to make operations research more
valuable by means of developing time saving heuristics and embedding models
into software that the average person can use.

This work represents the joining of both of my interests which I have been
developing throughout my education and serves as the capstone for my
collegiate career. I could not be more thankful for the people who have
helped me get to this point, especially my father who instilled in me the
work ethic required to gain the experience I needed to complete this project.

Moving forward, I'd like to continue developing products, like this one, that
will allow any person, regardless of background in math or computing, to
leverage optimization methods for providing decision support. For any
questions or inquiries, please feel free to reach me at the contact
information below.

Thank you for taking the time to read my work.

Sean Kelley

217-722-2228

seanpkelley94@gmail.com
