``dimrot.f``: Domain Integral ROTation
======================================

Description
------------

call ``dimrot( c, crdmap, u, dstmap, debug_driver, out )``

    compute the 3x3 global -> crack front local rotation

Output:

    ``j_data.domain_origin`` INTEGER

    ``j_data.domain_rot(3,3)`` DOUBLE PRECISION (3,3)

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *    dimrot.f                                                 *
    c *        -dimrot_coord_displ                                  *
    c *              -di_trans_nodvals                              *
    c *        -dimrott                                             *
    c *              -di1dsf.f                                      *
    c *              -difrts                                        *
    c *                                                             *
    c ***************************************************************

Procedure
----------

1. gather coordinates and displacements of crack-front nodes

2.1 if only one node on the crack front. we have the simple 2-D case. rotation matrix determined only by direction cosines. global-z and crack-z are assumed to coincide.

call ``dimrot_coord_displ``

2.2 if more than one node on front specified. we have a 3-D situation. get the position of the front-node to be used as the domain origin. for domain types 'a', 'b' and 'c', (types 1, 2 and 3, respectively), take the domain origin as the location where the crack-front advance is maximum (where the q-value equals 1.0). for domain type 'd' (uniform crack advance) take the domain origin as the first front node.

3. based on the number of front nodes, interpolation order, and q-function type construct a unit tangent vector to the front that lies in the crack plane (crack-z direction). crack-y is given by direction cosines of normal to crack plane. construct crack-x and final 3x3 structure-to-crack rotation by cross-product.

3a. for q-function type 'd' (=4), we just use the first two nodes on the front to find the tangent vector. this case is for a uniform crack advance along the full front.

alternatively, the user may have defined components of the crack front tangent vector rather than using the computed value. this feature helps, for example, when the mesh has the crack front intersecting a symmetry plane at other than 90-degrees. A correct user-defined tangent vector restores domain independent J-values.

call ``dimrott``

4. get crack front (unit) tangent vector (crack-z). compute crack-x unit vector from z and y. fill in global-to-crack rotation matrix.

call ``dimrot_coord_displ``

Call ``dimrot_coord_displ``
----------------------------

rotate coordinates of crack-front nodes to local system.

if necessary, transform nodal displacement values from constraint-compatible coordinates to global coordinates. then rotate displacements from global to local coordinates

call ``di_trans_nodvals``

    subroutine to transform a 3x1 vector of constraint-compatible-coordinate-system values to global-coordinate-system values. the routine premultiplies the incoming constraint-compatible values by the transpose of the stored global-to-constraint coordinate system rotation matrix.

Call ``dimrott``
-----------------

unit tangent vector to front directed along crack local-Z direction

Output:

    ``tvec(3)``

::

    c             compute or load unit tangent vector to the crack
    c             front according to the situation indicated by the
    c             case number or the user-defined flag.
    c             for cases 2,5,6 we average two tangents
    c             computed for adjacent line segments. for cases 1,3,4 we
    c             compute a single tangent at start or end of line segment.
    c             fnodes is list of structure nodes defining the segment(s).
    c
    c             tangent_vector_defined = .T. --> the user specified the
    c                                              tangent vector to use.
    c                                              components are in
    c                                              crack_front_tangent
    c                                              skip computations.
    c
    c
    c             case no.           situation
    c             --------           ---------
    c                1               linear; 2-nodes on front
    c                2               linear; 3-nodes on front
    c                3               quadratic; 3-nodes on front
    c                4               cubic; 4-nodes on front
    c                5               quadratic; 5-nodes on front
    c                6               cubic; 7-nodes on front
    c
    c             fnctyp: 1-4, used for cases 3,4 to determine if
    c                     tangent is needed for first or last node
    c                     on front. = 1 (first node), =3 (last node).
    c
    c             status: returned value = 0 (ok) = 1 (bad data)
    c
    c             case 1,3,4: 2, 3 or four nodes on front but only a single
    c                         element (linear, quadratic or cubic). compute
    c                         tangent at first or last node (2,3 or 4). use
    c                         utility routine to get derivatives of the 1-D
    c                         isoparametric shape functions.
    c
    c             case 2,5,6: two linear, quadratic or cubic segments
    c                         connecting 3, 5 or 6 front nodes.
    c                         compute average tangent at center (common)
    c                         node. compute two unit tangents, average
    c                         components, then restore unit length.

Call ``di1dsf.f``
------------------

calculates shape function values and derivatives for 1-dimension

Output:

    ``sf(3)`` - shape function values

    ``dsf(3)`` - derivatives of shape function