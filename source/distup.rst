``distup.f``: Domain Integral SeT UP
======================================

Description
------------

call ``distup( c, crdmap, nonode, debug_driver, out )``

set_up_domain:

    set up the domain for processing find coincident front nodes and set their q-values

Procedure
----------

1. for automatic domain definitions, set the q-values at user specified front nodes based on the domain type and the user specified front interpolation order.

2. if linear interpolation of q-values over the domain is on, we also need to interpolate q-values along front for quadratic elements.

3. look at first 1 or 2 nodes specified to be on the crack front. compute a distance to be used for locating all other nodes conincident with each specified crack front node. set the "box" size for proximity checking as a fraction of the distance measure.

4. loop over each specified front node. locate all other nodes in model that are conincident with the front node. (use the box test). set the q-value of each coincident node to be same as user specified value for the specified front node. dump verification list if user requested it.