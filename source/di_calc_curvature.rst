``di_calc_curvature``: Domain Integral CALculate CURVATURE
=============================================================

Description
------------

Domain Integral CALculate CURVATURE:
    calculate coefficients of curve described by crack front nodes.

::

    c             determine if crack front is curved.
    c             find coefficients of a quadratic expression defined by
    c             three crack front nodes: x = az^2 + bz + c
    c             if 'a' equals zero +/- tolerance, consider the front
    c             nodes to be colinear, and exit.
    c
    c             for a circular crack, find center and radius of a
    c             circle defined by the first three crack-front nodes.

Output:

    ``j_data.crack_curvature`` DOUBLE PRECISION (7)

::

    c             contents of array 'crack_curvature':
    c
    c             crack_curvature(1) = 0 for straight front
    c                                = 1 for curved front
    c             crack_curvature(2) = 0 for unknown curvature
    c                                = 1 for for a circular curve
    c             crack_curvature(3) = local x coordinate for circle
    c                                = coefficient 'a' for polynomial
    c             crack_curvature(4) = local z coordinate for circle
    c                                = coefficient 'b' for polynomial
    c             crack_curvature(5) = circle radius for circle
    c                                = coefficient 'c' for polynomial
    c             crack_curvature(6) = coefficient 'd' for polynomial
    c             crack_curvature(7) = coefficient 'e' for polynomial

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *    di_calc_curvature                                        *
    c *        -di_calc_coefficients                                *
    c *                                                             *
    c ***************************************************************





curvature:

    q =   ( x3 - x2 ) / ( ( z3 - z2 ) * ( z3 - z1 ) )
     &       - ( x1 - x2 ) / ( ( z1 - z2 ) * ( z3 - z1 ) )

circular curve:

    x_center = (   ( x1*x1 + z1*z1 ) * ( z3 - z2 )
     &                + ( x2*x2 + z2*z2 ) * ( z1 - z3 )
     &                + ( x3*x3 + z3*z3 ) * ( z2 - z1 ) )
     &            / ( two * (   x1 * ( z3 - z2 )
     &                        + x2 * ( z1 - z3 )
     &                        + x3 * ( z2 - z1 ) ) )

    z_center = (   ( x1*x1 + z1*z1 ) * ( x3 - x2 )
     &                + ( x2*x2 + z2*z2 ) * ( x1 - x3 )
     &                + ( x3*x3 + z3*z3 ) * ( x2 - x1 ) )
     &            / ( two * (   z1 * ( x3 - x2 )
     &                        + z2 * ( x1 - x3 )
     &                        + z3 * ( x2 - x1 ) ) )

    circle_radius = (   (x1 - x_center)**two
     &                     + (z1 - z_center)**two )**half

3-node curve:

    a =   ( x3 - x2 ) / ( ( z3 - z2 ) * ( z3 - z1 ) )
     &          - ( x1 - x2 ) / ( ( z1 - z2 ) * ( z3 - z1 ) )

    b = ( x1 - x2 + a * ( z2*z2 - z1*z1 ) ) / ( z1 - z2 )

    c =   x1 - a * z1*z1 - b * z1

5-node curve:

    call least-squares regression subroutine ``di_calc_coefficients`` to compute coefficients of polynomial.

Call ``di_calc_coefficients``
------------------------------

subroutine to compute coefficients of a fourth-order polynomial by a linear least-squares regression of the five data points of the five crack-front nodes.

or:

solve a linear set of simultaneous equations using gauss elimination with partial pivoting. one rhs is permitted. the coefficient matrix may be non-symmetric. the coefficient matrix is replaced by it's triangulated form.

solves: [a] {x} = {b}::

    c *        a       -- coefficient matrix of                          *
    c *        x       -- vector to receive computed solution            *
    c *        b       -- vector containing the right hand side          *
    x = [e d c b a]