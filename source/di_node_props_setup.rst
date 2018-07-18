``di_node_props_setup``: Domain Integral NODE PROPertieS SETUP
================================================================

Description
------------

``call di_node_props_setup (1, nonode, nelblk )``

di_node_props_setup:

    obtain alpha values at nodes. for temperature-dependent properties, also compute e and nu values at nodes. this routine replaces di_expan_coeff_setup, and the routine it calls, di_node_props, replaces di_node_expan_coeff.

Output:

    ``j_data.count_alpha`` INTEGER (nonode) ALLOCATABLE SAVE

    ``j_data.snode_alpha_ij`` REAL (nonode,6) ALLOCATABLE SAVE

    ``j_data.seg_snode_e`` REAL (nonode) ALLOCATABLE SAVE

    ``j_data.seg_snode_nu`` REAL (nonode) ALLOCATABLE SAVE

    ``j_data.block_seg_curves`` LOGICAL (nellk) ALLOCATABLE SAVE

    ``j_data.process_temperatures`` LOGICAL


Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *       -di_node_props_setup                                  *
    c *            -di_node_props                                   *
    c *                 -di_fgm_alphas                              *
    c *                 -di_constant_alphas                         *
    c *                 -di_seg_alpha_e_nu                          *
    c *                                                             *
    c ***************************************************************

Call ``di_node_props``
-----------------------

di_node_props:

    build average nodal values of material properties at each node in the model. all values are single-precision reals because double-precision accuracy is unnecessary.


loop through element blocks.
check type of property assignment in each block.

::

    property assignment: element nodes (fgm)   = 1
                         constant in element   = 2
                         temperature dependent = 3

1. check for fgm alphas. the identifier 'fgm_mark' for fgms assigned in inmat.f is -99.0. elprp.f then assigns this value to props(9,*), props(13,*) and props(34,*)

::

    call di_fgm_alphas( span, first_elem, count_alpha, snode_alpha_ij )
    c **********************************************************************
    c *                                                                    *
    c * di_fgm_alphas - retrieve nodal values of alpha assigned through    *
    c *                 'fgm' input.                                       *
    c *                                                                    *
    c **********************************************************************

2. check for alphas that are constant throughout element. (this is check #2 because fgm assignment of alpha places '-99' in the props array.)

::

    call di_constant_alphas( span, first_elem, count_alpha, snode_alpha_ij )
    c **********************************************************************
    c *                                                                    *
    c * di_constant_alphas - retrieve element values of alpha assigned     *
    c *                      through element definitions, and add values   *
    c *                      to nodal sums. the average nodal value is     *
    c *                      then computed in the calling routine.         *
    c *                                                                    *
    c **********************************************************************

3. check for temperature-dependent material properties assigned using segmental curves. curve_set_type

::

    curve_set_type =0, temperature-independent properties
                   =1, temperature-dependent properties
                   =2, strain-rate dependent properties

::

    call di_seg_alpha_e_nu( curve_set, num_curves_in_set,
        &                   first_curve, curve_set_type, span, first_elem,
        &                   count_alpha, snode_alpha_ij,
        &                   seg_snode_e, seg_snode_nu )
    c **********************************************************************
    c *                                                                    *
    c * di_seg_alpha_e_nu - retrieve nodal values of alpha, e and nu       *
    c *                     assigned through temperature-dependent         *
    c *                     segmental curves. to simplify logic, nodal     *
    c *                     alpha values are added to sum and averaged     *
    c *                     in calling routine. e and nu values at nodes   *
    c *                     are not averaged.                              *
    c *                                                                    *
    c **********************************************************************


