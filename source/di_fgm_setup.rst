``di_fgm_setup``: Domain Integral FGM SETUP
=============================================

Description
------------

``call di_fgm_setup ( 1, nonode, out, temperatures )``

di_fgm_setup:

    allocate data structures for two terms used in the calculation of the derivative of the stress work density. these are: nodal values of stress work density and strain.

Output:

    ``j_data.extrap_counts`` INTEGER (nonode) ALLOCATABLE SAVE

    ``j_data.swd_at_nodes`` (nonode) ALLOCATABLE SAVE

    ``j_data.strain_at_nodes`` DOUBLE PRECISION (6,nonode) ALLOCATABLE SAVE

    ``j_data.fgm_e`` LOGICAL

    ``j_data.fgm_nu`` LOGICAL

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *       -di_fgm_setup                                         *
    c *            -di_nod_vals                                     *
    c *                 -di_extrap_to_nodes                         *
    c *                      -ndpts1.f                              *
    c *                      -oulg1.f (oulgf)                       *
    c *                                                             *
    c ***************************************************************

Call ``di_nod_vals``
------------------------

``call di_nod_vals( extrap_counts, swd_at_nodes, strain_at_nodes )``

build average nodal values of the strain and stress work density for all nodes in the model.

::

    c                build average nodal values of the stress work density
    c                (swd) and strains in the model. for each element,
    c                shape functions will be used to extrapolate integration-
    c                point values to each of the element's nodes where they
    c                are then averaged.



Call ``di_extrap_to_nodes``
-----------------------------

subroutine to extrapolate strain and stress work density values from integration points to nodes for one element.

::

    c                       loop over element nodes. for 8 and 20-noded
    c                       hex elements using 2x2x2 integration, we
    c                       extrapolate to the nodes using the lagrangian
    c                       polynomials. otherwise we just average the
    c                       integration-point values and use that value
    c                       at every node. warp3d uses the following
    c                       arguments to describe elements and integration
    c                       order:
    c
    c                       etype = 1: 20-noded brick
    c                       etype = 2:  8-noded brick
    c                       etype = 3: 12-noded brick
    c                       etype = 4: 15-noded brick
    c                       etype = 5:  9-noded brick
    c
    c                       etype = 1, int_order = 1: 27 point rule (not used)
    c                       etype = 1, int_order = 8:  8 point rule
    c                       etype = 1, int_order = 9: 14 point rule
    c                       etype = 2, int_order = 1:  8 point rule
    c                       etype = 2, int_order = 2:  6 point rule (not used)