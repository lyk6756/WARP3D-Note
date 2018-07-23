``dielem``: Domain Integral ELEMent
====================================

::

    c   defined in dielem_a.f
    subroutine dielem ( coord, qvals, edispl, evel, eaccel,
     &                    feload, estress, history,
     &                    props, iprops, lprops, erots, nrow_hist,
     &                    rotate, iout, e_jresults, e_node_temps,
     &                    temperatures, enode_alpha_ij, ierr, elemno,
     &                    gdebug, one_point, geonl, numrows_stress,
     &                    snodes, elem_nod_swd, elem_nod_strains,
     &                    omit_ct_elem, fgm_e, fgm_nu, e_strain,
     &                    ym_nodes, ym_front_node, nu_nodes,
     &                    nu_front_node, front_nodes, num_front_nodes,
     &                    front_coords, domain_origin, domain_type,
     &                    front_order, e_iresults, comput_i, comput_j,
     &                    cf_traction_flags, cf_tractions,
     &                    front_elem_flag, expanded_front_nodes,
     &                    myid, numprocs, crack_curvature, face_loading,
     &                    seg_curves_flag, process_temperatures,
     &                    max_exp_front )

    c   called by dicmj
    call dielem( e_coord, q_element, e_displ, e_vel, e_accel,
     &                e_force, e_stress, elem_hist, e_props, e_props,
     &                e_props, e_rots, hist_size, domain_rot, iout,
     &                e_jresults, e_node_temps, elem_temps, e_alpha_ij,
     &                ierr, element, debug_elements, one_point_rule,
     &                geonl, numrow_sig, snodes, elem_nod_swd,
     &                elem_nod_strains, omit_ct_elem, fgm_e, fgm_nu,
     &                e_strain, e, e_front, nu, nu_front, front_nodes,
     &                num_front_nodes, front_coords, domain_origin,
     &                domain_type, front_order, e_iresults, comput_i,
     &                comput_j, cf_traction_flags, cf_load,
     &                front_elem_flag, expanded_front_nodes,
     &                myid, numprocs, crack_curvature, face_loading,
     &                seg_curves_flag, process_temperatures,
     &                max_exp_front )

Description
------------

* domain integral for 3-d isoparametric elements. supports:
    * finite strains-rotations
    * body forces (including inertia)
    * crack-face tractions
    * temperature loads
    * kinetic energy terms
    * anisotropic thermal expansion coefficients
    * nonhomogeneous material properties

* interaction integral for 3-d isoparametric elements. supports:
    * linear-elastic material behavior
    * crack-face tractions
    * nonhomogeneous material properties

::

    c ****************************************************************
    c *                                                              *
    c *         element name      type no.         description       *
    c *         ------------      --------         -----------       *
    c *                                                              *
    c *         q3disop              1         20 node brick(*)      *
    c *         l3dsiop              2          8 node brick         *
    c *         ts12isiop            3         12 node brick         *
    c *         ts15isiop            4         15 node brick         *
    c *         ts9isiop             5          9 node brick         *
    c *                                                              *
    c ****************************************************************

::

    c ****************************************************************
    c *                                                              *
    c *           strain-stress ordering in warp3d vectors:          *
    c *           ----------------------------------------           *
    c *                                                              *
    c *              eps-x, eps-y, eps-z, gam-xy, gam-yz, gam-xz     *
    c *              sig-x, sig-y, sig-z, tau-xy, tau-yz, tau-xz     *
    c *                                                              *
    c ****************************************************************

Inputs
-------

* ``coord``: ``e_coord(3,num_enodes)``: element nodal coordinates
* ``qvals``: ``q_element(num_enodes)``: element nodal q-values
* ``edispl``: ``e_displ(3,num_enodes)``: element nodal displacements
* ``evel``: ``e_vel(3,num_enodes)``: element nodal velocities
* ``eaccel``: ``e_accel(3,num_enodes)``: element nodal accelerations
* ``feload``: ``e_force(num_enodes)``: element nodal force
* ``estress``: ``e_stress(nstrs * num_gpts)``: element stresses at gauss points
* ``history``: ``elem_hist(hist_size * num_gpts)``: element histories at gauss points
* ``props``: ``e_props(mxelpr)`` ( ``=props(mxelpr,elemno)`` ): element properties
* ``iprops``: ``e_props(mxelpr)`` ( ``=props(mxelpr,elemno)`` ): element properties
* ``lprops``: ``e_props(mxelpr)`` ( ``=props(mxelpr,elemno)`` ): element properties
* ``erots``: ``e_rots(9*num_gpts)``: 3x3 rotation matrices from polar decompositions at each gauss point of element for geonl
* ``nrow_hist``: ``hist_size``: history size at each gauss point
* ``rotate``: ``domain_rot(3,3)``: global -> crack front local rotation matrix
* ``iout``: ``iout``: output file handle
* ``e_node_temps``: ``e_node_temps`` element nodal temperatures
* ``temperatures``: ``elem_temps`` element temperatures
* ``enode_alpha_ij``: ``e_alpha_ij(6,num_enodes)``: element nodal thermal expansion coefficients
* ``ierr``: ``ierr`` error code
* ``elemno``: ``element`` current element number
* ``gdebug``: ``debug_elements``: debug flags for domain integral computations
* ``one_point``: ``one_point_rule``: use one point rule flag

    ``.true.`` for use 1 point integration for 8-node elements
* ``geonl``: ``geonl`` ( ``=lprops(18,elemno)`` ) geometric nonlinearity flag
* ``numrows_stress``: ``numrow_sig`` ( ``=nstrs=9`` ) number of stress values per strain point in arrays passed by warp
* ``snodes``: ``snodes(num_enodes)``: element nodal numbers
* ``elem_nod_swd``: ``elem_nod_swd(num_enodes)``: element nodal values of stress work density
* ``elem_nod_strains``: ``elem_nod_strains(6,num_enodes)``: element nodal strains
* ``omit_ct_elem``: ``omit_ct_elem`` omit current element flag
* ``fgm_e``: ``fgm_e`` 'fgm' values of e flag

    ``.true.`` for 'fgm' values of e have been assigned at the nodes
* ``fgm_nu``: ``fgm_nu`` 'fgm' values of nu flag

    ``.true.`` for 'fgm' values of nu have been assigned at the nodes
* ``e_strain``: ``e_strain(9,num_gpts)``: element strains at gauss points in tensor form
* ``ym_nodes``: ``e(num_enodes)``: element nodal Young's modulus
* ``ym_front_node``: ``e_front`` young's modulus at point on front for homogeneous material
* ``nu_nodes``: ``nu(num_enodes)``: element nodal Poisson's ratio
* ``nu_front_node``: ``nu_front`` poisson's ratio at point on front for homogeneous material
* ``front_nodes``: ``front_nodes(num_front_nodes)`` numbers of front nodes
* ``num_front_nodes``: ``num_front_nodes`` numbers of front nodes
* ``front_coords``: ``front_coords(3,num_front_nodes)`` coordinates of crack-front nodes
* ``domain_origin``: ``domain_origin`` position of domain origin in the list ``front_nodes``
* ``domain_type``: ``domain_type``: domain type of q functions a-d
* ``front_order``: ``front_order``: order of crack front interpolation
* ``comput_i``: ``comput_i``: compute interaction inetgral flag

    ``.true.`` for compute interaction inetgral
* ``comput_j``: ``comput_j``: compute j inetgral flag

    ``.true.`` for compute j inetgral
* ``cf_traction_flags``: ``cf_traction_flags``: crack face traction flag

    ``.true.`` for compute crack face load terms
* ``cf_tractions``: ``cf_load(3)``: crack face load
* ``front_elem_flag``: ``front_elem_flag``: front element flag

    ``.true.`` for current element is front element
* ``expanded_front_nodes``: ``expanded_front_nodes``:
* ``myid``: ``myid``:
* ``numprocs``: ``numprocs``:
* ``crack_curvature``: ``crack_curvature(7)``: coefficients of curve described by crack front nodes. (see subroutine ``di_calc_curvature``)
* ``face_loading``: ``face_loading``: flag
* ``seg_curves_flag``: ``seg_curves_flag``: temperature loading flag

    ``.true.`` for have temperature loading
* ``process_temperatures``: ``process_temperatures``: process temperatures flag

    ``.true.`` for temperature depended process
* ``max_exp_front``: ``max_exp_front`` ( ``=200`` ): maximum number of front nodes list

Outputs
-------

* ``e_jresults``: ``e_jresults(8)``
* ``e_iresults``: ``e_iresults(8,8)``

Calling Tree
-------------

::

    c ****************************************************************
    c *                                                              *
    c *    (dielem_a.f)                                              *
    c *       -dielem                                                *
    c *           -digetr                                            *
    c *           -diqmp1                                            *
    c *           -getgpts (getgpts.f)                               *
    c *           -derivs (derivs.f)                                 *
    c *           -dielcj                                            *
    c *           -dieler                                            *
    c *           -digrad                                            *
    c *           -dielrv                                            *
    c *           -dieliq                                            *
    c *           -dielrt                                            *
    c *           -dippie                                            *
    c *           -shapef (shapef.f)                                 *
    c *           -dielav                                            *
    c *           -di_calc_j_terms                                   *
    c *                                                              *
    c *    (dielem_b.f)                                              *
    c *           -di_calc_r_theta                                   *
    c *               -di_calc_distance                              *
    c *           -di_calc_constitutive                              *
    c *           -di_calc_aux_fields_k                              *
    c *           -di_calc_aux_fields_t                              *
    c *           -di_calc_i_terms                                   *
    c *                                                              *
    c *    (dielem_c.f)                                              *
    c *           -di_calc_surface_integrals                         *
    c *               -dielwf                                        *
    c *               -dielrl                                        *
    c *               -di_calc_surface_j                             *
    c *               -di_calc_surface_i                             *
    c *                   -di_reorder_nodes                          *
    c *                                                              *
    c ****************************************************************

Procedure
----------

* set up basic element properties. load all six thermal expansion coefficients from material associated with the element. if they are all zero, we skip processing of element for temperature loading. all temperature terms in domain involve derivatives of temperature at gauss points and expansion coefficients at gauss points. if specified coefficients for the element are zero, we skip temperature processing of elements.

* for 'fgm' alpha values, elem_alpha(1)-elem_alpha(3) will receive a value of -99.0. in the loop over integration points, subroutine dielav changes elem_alpha to the value of alpha at the integration point by interpolating nodal alpha values. to be consistent with the solution of the boundary-value problem, for linear-displacement elements, dielav will assign to elem_alpha the average of nodal alpha values.

* set integration order info for volume integrals.

* set number of stress values per strain point in arrays passed by warp

* set up data needed at all integration points and nodes needed for the volume integrals and crack face traction integrals:

1. transform unrotated Cauchy stresses to Cauchy stresses for geonl formulation. estress on input contains unrotated stresses. first convert to Cauchy stresses then to 1 st PK stresses. these are in global coordinates. note: 1 PK stresses are non-symmetric so we keep the 3x3 tensor stored as a vector. position 10 is for the work density. the work density is scaled by det F to refer to t=0 configuration.

2. for small-displacement formulation, copy the input (symmetric) stresses (6x1) into 3x3 tensor form.

::

    do ptno = 1, ngpts
        loc = ( ptno-1 )  * numrow
        stresses(1,ptno)  = estress(loc+1)
        stresses(2,ptno)  = estress(loc+4)
        stresses(3,ptno)  = estress(loc+6)
        stresses(4,ptno)  = estress(loc+4)
        stresses(5,ptno)  = estress(loc+2)
        stresses(6,ptno)  = estress(loc+5)
        stresses(7,ptno)  = estress(loc+6)
        stresses(8,ptno)  = estress(loc+5)
        stresses(9,ptno)  = estress(loc+3)
        stresses(10,ptno) = estress(loc+7)
    end do

3. copy engineering strains into 3x3 tensor form. change gamma strains to tensor strains for off-diagonal terms. this formulation is not correct for large strains.

::

    do enode = 1, nnode
        strains(1,enode) = elem_nod_strains(1,enode)
        strains(2,enode) = elem_nod_strains(4,enode)/2.0
        strains(3,enode) = elem_nod_strains(6,enode)/2.0
        strains(4,enode) = elem_nod_strains(4,enode)/2.0
        strains(5,enode) = elem_nod_strains(2,enode)
        strains(6,enode) = elem_nod_strains(5,enode)/2.0
        strains(7,enode) = elem_nod_strains(6,enode)/2.0
        strains(8,enode) = elem_nod_strains(5,enode)/2.0
        strains(9,enode) = elem_nod_strains(3,enode)
    end do

4. rotate nodal coordinates, displacements, velocities and accelerations to crack front normal coordinate system.

call ``dielrv``: Domain Integral ELement Rotate Vector

::

    call dielrv( coord, cdispl, cvel, caccel, edispl, evel, eaccel,
     &             rotate, nnode, iout, debug )
    c *******************************************************************
    c *                                                                 *
    c *   rotate nodal vector values to crack x-y-z                     *
    c *                                                                 *
    c *******************************************************************

* ``cdispl(3,20)`` nodal displacements at crack x-y-z
* ``cvel(3,20)`` nodal velocities at crack x-y-z
* ``caccel(3,20)`` nodal accelerations at crack x-y-z

5. modify the nodal q-values to reflect linear interpolations for 20, 15, 12-node elements.

call ``dieliq``: Domain Integral ELement Interpolate Q-values

::

    call dieliq( qvals, debug, iout, nnode, etype, elemno, snodes,
     &             coord, front_elem_flag, num_front_nodes, front_nodes,
     &             front_coords, expanded_front_nodes, domain_type,
     &             domain_origin, front_order, qp_node, crack_curvature,
     &             max_exp_front)
    c ******************************************************************
    c *                                                                *
    c *         interpolate q-values at side nodes                     *
    c *                                                                *
    c *         if any 1/4-point node is detected, special integration *
    c *         for crack-face traction on crack-front element faces   *
    c *         is not used.                                           *
    c *                                                                *
    c ******************************************************************

6. compute coordinate jacobian at all gauss points and the center point of element. these are now in cracked coordinates.

6a. isoparametric coordinates of gauss point

::

    call getgpts( etype, order, ptno, xsi, eta, zeta, weight )
    c     ****************************************************************
    c     *                                                              *
    c     *     given the element type, order of integration and the     *
    c     *     integration point number, return the parametric          *
    c     *     coordinates for the point and the weight value for       *
    c     *     integration                                              *
    c     *                                                              *
    c     ****************************************************************

    call derivs( etype, xsi, eta, zeta, dsf(1,1,ptno),
     &               dsf(1,2,ptno), dsf(1,3,ptno) )
    c     ****************************************************************
    c     *                                                              *
    c     *     given the element type, integration point coordinates    *
    c     *     return the parametric derivatives for each node          *
    c     *     shape function                                           *
    c     *                                                              *
    c     ****************************************************************

6b. coordinate jacobian, it's inverse, and determinant.

call ``dielcj``: Domain Integral ELement Compute Jacobian

::

    call dielcj( dsf(1,1,ptno), coord, nnode, jacob(1,1,ptno),
     &               jacobi(1,1,ptno), detvol(ptno), ierr, iout, debug )
    c *******************************************************************
    c *                                                                 *
    c *    compute coordinate jacobian, its determinate, and inverse    *
    c *                                                                 *
    c *******************************************************************

* ``jacob(3,3,28)`` coordinate jacobian matrix at integration points
* ``jacobi(3,3,28)`` coordinate inverse jacobian matrix at integration points
* ``detvol(28)`` determinant of jacobian matrix at integration points

7. rotate stresses and strains to crack-front coordinates at all gauss points. get the stress work density from warp results.

call ``dielrt``: Domain Integral ELement Rotate Tensor

::

    call dielrt( ptno, rotate, stresses(1,ptno), csig(1,ptno),
     &               1, iout, debug )
    call dielrt( ptno, rotate, e_strain(1,ptno), ceps_gp(1,ptno),
     &               2, iout, debug )
    c *******************************************************************
    c *                                                                 *
    c *  rotate stresses or strains from global->crack x-y-z            *
    c *                                                                 *
    c *******************************************************************

* ``csig(10,27)``: stress in crack-front coordinates at gauss points
* ``ceps_gp(9,27)``: strain in crack-front coordinates at gauss points

8. rotate strains to crack-front coordinates at all nodes.

::

    call dielrt( enode, rotate, strains(1,enode), ceps_n(1,enode),
     &        3, iout, debug )

* ``ceps_n(9,20)``: nodal strain in crack-front coordinates

9. process temperature (inital) strains if required. rotate the thermal expansion coefficients from from global->crack coordinates in case they are anisotropic. elem_alpha(1:6) in global is replaced by elem_alpha(1:6) in crack. rotate values that are constant over the element (elem_alpha) and values at each node of the element (enode_alpha_ij). (nodal values enable computation of the x1 derivative of the alpha_ij term in J.) for isotropic CTEs this is some unnecessary work.

::

    call dippie( rotate, iout, debug, elem_alpha )
    c *******************************************************************
    c *                                                                 *
    c *  set up to process temperature effects for domain integral      *
    c *                                                                 *
    c *******************************************************************

10. for single point integration of bricks, compute average stresses, energy density, and strain at center of element. set number of gauss points to one to control subsequent loop.

* loop over all gauss points and compute each term of the domain integral for j, and the interaction integral for i.

::

    c             jterm(1) :  work density
    c             jterm(2) :  traction - displacement gradient
    c             jterm(3) :  kinetic energy denisty
    c             jterm(4) :  accelerations
    c             jterm(5) :  crack face loading (handled separately)
    c             jterm(6) :  thermal strain (2 parts. set to zero for an fgm)
    c             jterm(7) :  stress times partial of strain wrt x1 (for fgms)
    c             jterm(8) :  partial of stress work density wrt x1 (for fgms)
    c             (jterms 7 & 8 are used to replace the explicit partial
    c              derivative of stress work density wrt x1 (for fgms))
    c
    c             iterm(1) :  stress * derivative of aux displacement
    c             iterm(2) :  aux stress * derivative of displacement
    c             iterm(3) :  mixed strain energy density (aux stress * strain)
    c             iterm(4) :  first term of incompatibility (stress * 2nd deriv
    c                         of aux displacement
    c             iterm(5) :  second term of incompatibility (stress * deriv
    c                         of aux strain)
    c             iterm(6) :  deriv of constitutive tensor * strain * aux strain
    c             iterm(7) :  (not yet verified)
    c                         aux stress * deriv of cte * relative change in temp
    c                       + aux stress * cte * deriv of relative change in temp
    c             iterm(8) :  crack face traction * aux disp. derivative

1. isoparametric coordinates of gauss point and weight.

::

    call getgpts( etype, order, ptno, xsi, eta, zeta, weight )

2. evaluate shape functions of all nodes at this point. used to find value of q function at integration point, the total velocity at the point, values of acceleration at the point, and alpha values at the point.

::

    call shapef( etype, xsi, eta, zeta, sf )

call ``dielav``: Domain Integral ELement gAuss point Values

::

    call dielav( sf, evel, eaccel, point_velocity, point_accel_x,
     &               point_accel_y, point_accel_z, qvals, point_q,
     &               nnode, point_temp, e_node_temps, elem_alpha,
     &               enode_alpha_ij, linear_displ, fgm_alphas, elemno,
     &               ym_nodes, nu_nodes, point_ym, point_nu, fgm_e,
     &               fgm_nu, coord, point_x, point_y, point_z,
     &               seg_curves_flag, iout )
    c *******************************************************************
    c *                                                                 *
    c *   get q, velocity, accelerations, temperature and alpha at      *
    c *   gauss point.                                                  *
    c *                                                                 *
    c *******************************************************************

* ``point_q``: q-valuse at gauss point
* ``point_velocity``: total velocity at gauss point
* ``kin_energy``: kinetic energy at gauss point
* ``point_accel_x, point_accel_y, point_accel_z``: accelerations at gauss point
* ``point_temp``: temperature at gauss point
* ``elem_alpha(6)``: alpha at gauss point
* ``point_ym``: young's modulus at gauss point
* ``point_nu``: poisson's ratio at gauss point
* ``point_x, point_y, point_z``: coordinates of gauss point in the local crack-front system

3. for this integration point, compute displacement derivatives, q-function derivatives and temperature derivative in crack coordinate system. strains will be used in the order eps11, eps22, eps33, eps12, eps23, eps13

* ``dux, dvx, dwx``: displacement derivatives with respect to x in crack coordinate system
* ``dqx, dqy, dqz``: q-function derivatives in crack coordinate system
* ``dtx``: temperature derivative with respect to x in crack coordinate system
* ``dalpha_x1(6)``: alpha derivative with respect to x in crack coordinate system
* ``dswd_x1``: stress work density derivative with respect to x in crack coordinate system
* ``dstrain_x1(9)``: nodal strain derivative with respect to x in crack coordinate system
* ``csig_dstrain_x1``:
* ``de_x1``: young's modulus derivative with respect to x in crack coordinate system
* ``dnu_x1``: poisson's ratio derivative with respect to x in crack coordinate system

4. call routines to compute domain-integral terms for current integration point.

4a. compute j terms

::

    call di_calc_j_terms(weight, evol, jterm, ptno, dqx,
     &                       dqy, dqz, dux, dvx, dwx, dtx, csig,
     &                       kin_energy, rho, point_q, point_accel_x,
     &                       point_accel_y, point_accel_z, point_temp,
     &                       process_temperatures, elem_alpha,
     &                       dalpha_x1, csig_dstrain_x1, dstrain_x1,
     &                       dswd_x1, omit_ct_elem, fgm_e, fgm_nu,
     &                       elemno, myid, numprocs, seg_curves_flag,
     &                       debug, iout)

4b. compute i terms

* perform edi evaluations for traction loaded faces for jterm(5) and then for iterm(8).

::

    call di_calc_surface_integrals( elemno, etype, nnode, snodes,
     &                             feload, cf_traction_flags,
     &                             cf_tractions, rotate, dsf, jacobi,
     &                             cdispl, qvals, coord, front_nodes,
     &                             front_coords, domain_type,
     &                             domain_origin, num_front_nodes,
     &                             front_order, ym_front_node,
     &                             nu_front_node, comput_j,
     &                             comput_i, jterm, iterm,
     &                             front_elem_flag, qp_node,
     &                             crack_curvature, face_loading, iout,
     &                             debug)
    c***********************************************************************
    c                                                                      *
    c    subroutine to drive computation of surface-traction               *
    c    integrals                                                         *
    c***********************************************************************

* save edi values in result vectors

Compute i terms
------------------

1. call ``di_calc_r_theta``: Domain Integral CALCulate Radius and angle THETA

    * for straight element edges:

    compute distance from integration point to the closest line that connects two adjacent front nodes.

    compute angle between integration point, line connecting front nodes, and projection of integration point onto crack plane.

    * for curved element edges:

    compute distance from integration point to the curve fitted through adjacent front nodes.

    compute angle between integration point, curve, and projection of integration point onto crack plane.

::

    call di_calc_r_theta( 2, front_nodes, num_front_nodes,
     &                        front_coords, domain_type, domain_origin,
     &                        front_order, point_x, point_y, point_z,
     &                        elemno, ptno, r, t, crack_curvature,
     &                        debug, iout )
    c ****************************************************************
    c                                                                *
    c subroutine to calculate radius "r", and angle "theta" of       *
    c a single point, measured in polar coordinates in the           *
    c local crack-front system.                                      *
    c                                                                *
    c ****************************************************************

* ``r``: radius "r" in polar coordinates in the local crack-front system
* ``t``: angle "theta" in polar coordinates in the local crack-front system

2. call ``di_calc_constitutive``: Domain Integral CALCulate CONSTITUTIVE tensor components

::

    call di_calc_constitutive( dcijkl_x1, sijkl, dsijkl_x1,
     &                             point_ym, point_nu, de_x1, dnu_x1,
     &                             elemno, debug, iout )
    c***************************************************************
    c                                                              *
    c    subroutine to calculate constitutive tensors for          *
    c    interaction integral                                      *
    c                                                              *
    c***************************************************************

* ``dcijkl_x1(3)``: constitutive tensor derivative
* ``sijkl(3)``: compliance tensor
* ``dsijkl_x1(3)``: compliance tensor derivative

::

    c             the three non-zero components of cijkl are:
    c                (1) lambda + 2*mu = e(1-nu)/((1+nu)(1-2nu))
    c                    d(1)/de  = (1-nu)/((1+nu)(1-2nu))
    c                    d(1)/dnu = 2e*nu(2-nu)/((1+nu)^2*(1-2nu)^2)
    c                (2) lambda = e*nu/((1+nu)(1-2nu))
    c                    d(2)/de  = nu/((1+nu)(1-2nu))
    c                    d(2)/dnu = e(1+2nu^2)/((1+nu)^2*(1-2nu)^2)
    c                (3) 2*mu = e/(1+nu)
    c                    d(3)/de  = 1/(1+nu)
    c                    d(3)/dnu = -e/(1+nu)^2
    c
    c             the three non-zero components of sijkl are:
    c                (1) d(1) = 1/e
    c                    d(1)/de = -1/e^2       d(1)/dnu = 0
    c                (2) d(2) = -nu/e
    c                    d(2)/de = nu/e^2       d(2)/dnu = -1/e
    c                (3) d(3) = (1+nu)/e
    c                    d(3)/de = -(1+nu)/e^2  d(3)/dnu = 1/e

3. call ``di_calc_aux_fields_k``: Domain Integral CALCulate AUXiliary FIELDS for stress intensity factors K

::

    call di_calc_aux_fields_k( elemno, ptno, r, t, ym_front_node,
     &                             nu_front_node, dcijkl_x1, sijkl,
     &                             dsijkl_x1, aux_stress,
     &                             aux_strain, daux_strain_x1,
     &                             du11_aux,  du21_aux,  du31_aux,
     &                             du111_aux, du112_aux, du113_aux,
     &                             du211_aux, du212_aux, du213_aux,
     &                             du311_aux, du312_aux, du313_aux,
     &                             iout )
    c***************************************************************
    c                                                              *
    c subroutine to calculate auxiliary stresses and displacements *
    c at element integration points using the expressions obtained *
    c by Williams: On the stress distribution at the base of a     *
    c stationary crack. Journal of Applied Mechanics 24, 109-114,  *
    c 1957.                                                        *
    c                                                              *
    c***************************************************************

* ``aux_stress(9,8)``: auxiliary stresses at integration point
* ``aux_strain(9,8)``: auxiliary strains at integration point
* ``daux_strain_x1(9,8)``: auxiliary strains derivatives
* ``du111_aux(8),du112_aux(8),du113_aux(8)``, ``du211_aux(8),du212_aux(8),du213_aux(8)``, ``du311_aux(8),du312_aux(8),du313_aux(8)``: second derivatives of displacement (uj,1i)

4. call ``di_calc_aux_fields_t``: Domain Integral CALCulate AUXiliary FIELDS for T-stresses

::

    call di_calc_aux_fields_t( elemno, ptno, r, t, ym_front_node,
     &                             nu_front_node, dcijkl_x1, sijkl,
     &                             dsijkl_x1, aux_stress,
     &                             aux_strain, daux_strain_x1,
     &                             du11_aux,  du21_aux,  du31_aux,
     &                             du111_aux, du112_aux, du113_aux,
     &                             du211_aux, du212_aux, du213_aux,
     &                             du311_aux, du312_aux, du313_aux,
     &                             iout )
    c***************************************************************
    c                                                              *
    c subroutine to calculate auxiliary stresses and displacements *
    c at integration points for t-stresses using the expressions   *
    c obtained by Michell for a point load on a straight crack:    *
    c (see Timoshenko and Goodier, Theory of Elasticity p. 110.,   *
    c  eq. 72 for stress sigma_r.)                                 *
    c                                                              *
    c***************************************************************

5. call ``di_calc_i_terms``: Domain Integral CALCulate Interaction integral terms for stress intensity factors

::

    call di_calc_i_terms( ptno, dqx, dqy, dqz, dux, dvx, dwx,
     &                        dtx, csig, aux_stress, ceps_gp,
     &                        aux_strain, dstrain_x1,
     &                        daux_strain_x1, dcijkl_x1,
     &                        du11_aux,  du21_aux,  du31_aux,
     &                        du111_aux, du211_aux, du311_aux,
     &                        du112_aux, du212_aux, du312_aux,
     &                        du113_aux, du213_aux, du313_aux,
     &                        process_temperatures, elem_alpha,
     &                        dalpha_x1, point_temp, point_q, weight,
     &                        elemno, fgm_e, fgm_nu, iterm,
     &                        iout, debug)
    c***************************************************************
    c                                                              *
    c    subroutine to calculate terms of i-integral               *
    c                                                              *
    c***************************************************************