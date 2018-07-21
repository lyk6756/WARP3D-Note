``dicmj.f``: Domain Integral CoMpute J
=======================================

Description
------------

domain_compute:

    drive execution of element routine to compute j and i-integrals for a single domain

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *       -dicmj.f                                              *
    c *            -di_trans_nodvals                                *
    c *            -vec_ops (zero_vector.f)                         *
    c *            -dielem                                          *
    c *                 -dielem_a.f                                 *
    c *                 -dielem_b.f                                 *
    c *                 -dielem_c.f                                 *
    c *                                                             *
    c ***************************************************************

Procedure
----------

1. loop over all structure elements. if element is involved in integral computations, call element dependent routine.

* build copy of element coordinates, displacements, velocities, accelerations, q-values from global data. zero load terms that correspond to constrained dof. if we have temperature loadings, get temps for element nodes (node values + element uniform value). rotate displacements, velocities and accelerations from constraint-compatible to global coordinates. obtain element nodal values of stress work density and strain from structure node-value arrays.

    * ``e_coord(3,num_enodes)``: element nodal coordinates
    * ``snodes(num_enodes)``: element nodal numbers
    * ``q_element(num_enodes)``: element nodal q-values
    * ``e_displ(3,num_enodes)``: element nodal displacements
    * ``e_vel(3,num_enodes)``: element nodal velocities
    * ``e_accel(3,num_enodes)``: element nodal accelerations
    * ``e(num_enodes)``: element nodal Young's modulus
    * ``nu(num_enodes)``: element nodal Poisson's ratio
    * ``e_force(num_enodes)``: element nodal force
    * ``e_node_temps(num_enodes)``: element nodal temperatures
    * ``e_alpha_ij(6,num_enodes)``: element nodal thermal expansion coefficients
    * ``elem_nod_swd(num_enodes)``: element nodal values of stress work density
    * ``elem_nod_strains(6,num_enodes)``: element nodal strains

* if necessary, transform nodal values from constraint-compatible coordinates to global coordinates.

::

    call di_trans_nodvals( e_displ(1,enode), trnmat(snode)%mat(1,1))
    call di_trans_nodvals( e_vel(1,enode), trnmat(snode)%mat(1,1))
    call di_trans_nodvals( e_accel(1,enode), trnmat(snode)%mat(1,1))

    c***************************************************************
    c                                                              *
    c subroutine to transform a 3x1 vector of constraint-          *
    c compatible-coordinate-system values to global-coordinate-    *
    c system values. the routine premultiplies the incoming        *
    c constraint-compatible values by the transpose of the stored  *
    c global-to-constraint coordinate system rotation matrix.      *
    c                                                              *
    c***************************************************************

* for problems with temperature loads, pull out temperatures of element nodes and set the thermal expansion coefficients at element nodes. remove reference temperatures of model nodes.

* crack-face tractions. the contribution to J arising from crack-face tractions is calculated element-by-element using the equivalent nodal loads on each element face computed during setup of load step for applied pressures, surface tractions and body forces. here we just get the element equiv. nodal forces. they are passed to element J routine which sorts out the loaded face. a similar procedure is used to compute the contribution to I.

::

    call vec_ops( e_force,
     &              eq_node_forces(eq_node_force_indexes(elemno)),
     &              dummy, 3*num_enodes, 5 )

    c     ****************************************************************
    c     *                                                              *
    c     *     perform simple vector-vector operations on single or     *
    c     *     double precision vectors (based on the port)             *
    c     *                                                              *
    c     ****************************************************************
    subroutine vec_ops( veca, vecb, vecc, n, opcode )
    c            opcode v:   a = b

* determine if we really need to process element based on q-values, nodal velocities, accelerations and specified temperature loadings.

* gather element stresses, histories, properties and 3x3 rotation matrices from polar decompositions at each gauss point of element for geonl

    * ``e_stress(nstrs * num_gpts)``: element stresses at gauss points
    * ``elem_hist(hist_size * num_gpts)``: element histories at gauss points
    * ``e_props(mxelpr)``: element properties
    * ``e_rots(9*num_gpts)``: rotation matrices

* gather element strains at integration points. and store in tensor form. convert engineering (total) strains of off-diagonal terms to tensor strains.

    * ``e_strain(9,num_gpts)``: element strains at gauss points in tensor form

* for crack-face loading, make a copy of the tractions input by the user in the domain definition. if none was input, dielem_c.f automatically uses the equivalent nodal loads used to solve the boundary value problem.

::

    cf_load(1) = cf_tractions(1)
    cf_load(2) = cf_tractions(2)
    cf_load(3) = cf_tractions(3)

* call the element routine to compute contribution to the j-integral and or i-integral for domain

::

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

2. print values as required based on user specified flags.

2a. J-integral results: calculate stress intensity factor K, from J

2b. interaction-integral results: calculate K from relationship with interaction integral

::

    c             the 8 terms for I-integral results are stored
    c             in array e_iresults(8,7) as follows:
    c
    c                               value    auxiliary field
    c
    c                  iiterms(i,1): KI       plane stress
    c                  iiterms(i,2): KI       plane stress
    c                  iiterms(i,3): KII      plane stress
    c                  iiterms(i,4): KII      plane stress
    c                  iiterms(i,5): KIII     anti-plane shear
    c                  iiterms(i,6): T11,T33  plane stress
    c                  iiterms(i,7): T11,T33  plane strain
    c                  iiterms(i,8): T13      anti-plane strain

2c. compute J-values from K-values

3. print sum of values from all elements in domain, and J, I for domain