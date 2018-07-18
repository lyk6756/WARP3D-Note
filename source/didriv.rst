``didriv.f``: Domain Integral DRIVer
======================================

Description
------------

domain driver:
    drive the computation of j-integral values using the domain integral technique.

    or:

    drive the computation of mixed-mode stress intensity factors and t-stress using the interaction integral.

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *    didriv.f                                                 *
    c *       -dickdm                                               *
    c *       -diheadr                                              *
    c *       -distup.f                                             *
    c *       -di_cf_elem                                           *
    c *       -dimrot.f                                             *
    c *            -dimrot_coord_displ                              *
    c *                 -di_trans_nodvals                           *
    c *            -dimrott                                         *
    c *                 -di1dsf.f                                   *
    c *                 -difrts                                     *
    c *       -di_calc_e33                                          *
    c *       -di_calc_curvature                                    *
    c *            -di_calc_coefficients                            *
    c *       -difrar.f (di_front_q_area)                           *
    c *            -di1dsf.f                                        *
    c *       -di_node_props_setup                                  *
    c *            -di_node_props                                   *
    c *                 -di_fgm_alphas                              *
    c *                 -di_constant_alphas                         *
    c *                 -di_seg_alpha_e_nu                          *
    c *       -di_fgm_setup                                         *
    c *            -di_nod_vals                                     *
    c *                 -di_extrap_to_nodes                         *
    c *                      -ndpts1.f                              *
    c *                      -oulg1.f (oulgf)                       *
    c *       -diexp4.f                                             *
    c *            -diexp4                                          *
    c *            -diexp13                                         *
    c *       -dicmj.f                                              *
    c *            -dielem_a.f                                      *
    c *            -dielem_b.f                                      *
    c *            -dielem_c.f                                      *
    c *       -di_write_std_out                                     *
    c *       -di_write_packets                                     *
    c *                                                             *
    c ***************************************************************

Procedure
----------

1. check validity of domain before starting computations.

2. perform exhaustive check on consistency of 1) number of front nodes, 2) front interpolation order, and 3) domain type.

call ``dickdm``::

    c ***************************************************************
    c *                                                             *
    c * domain_check  - exhaustive testing of domain defintiion for *
    c *                 consistency                                 *
    c *                                                             *
    c ***************************************************************

3. allocate space for a q-value at each structure node. allocate space for expanded lists of coincident front nodes at each front location.

4. output header information for domain

call ``diheadr``::

    c ***************************************************************
    c *                                                             *
    c * domain_header - output info at start of domain computations *
    c *                                                             *
    c ***************************************************************

5. when q-values are given by the user, expand the compressed list of q-values into a list of length number of structure nodes.

6. set up the domain. for automatic domains, set q-values at front nodes. then find coincident front nodes and set their q-values.

call ``distup.f``::

    c ***************************************************************
    c *                                                             *
    c * set_up_domain  -- set up the domain for processing          *
    c *                   find coincident front nodes and set their *
    c *                   q-values                                  *
    c *                                                             *
    c ***************************************************************

Output:

    ``j_data.q_values`` REAL (:) ALLOCATABLE SAVE

7. allocate a vector of logicals and assign .true. for each element connected to a crack front node.

call ``di_cf_elem``::

    c **********************************************************************
    c *                                                                    *
    c * di_cf_elem - create a logical vector whose entries are .true. for  *
    c *              elements incident on the crack tip, and .false. for   *
    c *              those that are not. dicmj will use this info to set a *
    c *              flag for each element that is analyzed by dielem. if  *
    c *              a user includes the domain integral command           *
    c *              'omit crack front elements for fgms yes', the flag    *
    c *              will cause terms7 and 8 to be set to zero.            *
    c *                                                                    *
    c **********************************************************************

Output:

    ``j_data.crack_front_elem`` LOGICAL (:) ALLOCATABLE SAVE

8. at point on front where integral is being computed, build the global->crack rotation matrix. gather coordinates and displacements of crack-front nodes, and rotate them to local crack-front system.

call ``dimrot.f``::

    c **********************************************************************
    c *                                                                    *
    c * dimrot - compute the 3x3 global -> crack front local rotation      *
    c *                                                                    *
    c **********************************************************************

Output:

    ``j_data.domain_origin`` INTEGER
    ``j_data.domain_rot(3,3)`` DOUBLE PRECISION (3,3)

8c. calculate strain e33 at node at domain origin. this is for T-stress calculations using the interaction integral

call ``di_calc_e33``::

    c *******************************************************************
    c *                                                                 *
    c *   calculate strain e33 at domain origin for T-stress calcs.     *
    c *   calculate strain e33 as the difference between the            *
    c *   deformed and undeformed crack-front lengths delta_L / L       *
    c *                                                                 *
    c *******************************************************************

8c. calculate properties of a curve passing through the front nodes. these will be used to compute distance 'r' from integration points to a curved crack front.

call ``di_calc_curvature`` from ::

    c *******************************************************************
    c *                                                                 *
    c *   calculate coefficients of curve described by crack front      *
    c *   nodes.                                                        *
    c *                                                                 *
    c *******************************************************************

9. compute area under the q-function over that part of crack front for this domain. the area must be >0 else fatal error in domain (user forgot to set q-values on front nodes)

call ``difrar.f``::

    c **********************************************************************
    c *                                                                    *
    c * di_front_q_area - compute area under q-function along front for    *
    c *                   this domain                                      *
    c *                                                                    *
    c **********************************************************************

10. set logical flags to indicate if the nodal velocities and accelerations are all zero for this load step. if so, some later computations can be skipped.

11. Build the node average value of thermal expansion coefficient. for temperature-dependent material properties, also build the node average value of young's modulus and poisson's ratio. for temperature-independent material properties, values of e and nu are obtained within dicmj.f. nodal properties are needed for domain integral computations to compute spatial derivatives within the domain.

call ``di_node_props_setup``::

    c **********************************************************************
    c *                                                                    *
    c * di_node_props_setup - obtain alpha values at nodes. for            *
    c *                       temperature-dependent properties, also       *
    c *                       compute e and nu values at nodes. this       *
    c *                       routine replaces di_expan_coeff_setup,       *
    c *                       and the routine it calls, di_node_props,     *
    c *                       replaces di_node_expan_coeff.                *
    c **********************************************************************

12. Build the nodal averages of strain energy density (stress work density) and strains. These terms are used to calculate the derivative of the strain energy density, which appears in the domain integral when material properties vary spatially (e.g. fgms). The nodal values calculated in di_fgm_setup will be used to compute their spatial derivatives at integration points.

call ``di_fgm_setup``::

    c **********************************************************************
    c *                                                                    *
    c * di_fgm_setup - allocate data structures for two terms used         *
    c * in the calculation of the derivative of the stress work density.   *
    c * these are: nodal values of stress work density and strain.         *
    c *                                                                    *
    c **********************************************************************

12b. at point on front where integral is being computed, collect young's modulus and poisson's ratio. this assumes that all elements connected to this crack-front node have identical, homogeneous material properties, or that fgm material properties have been assigned to the model. for homogeneous material, "props" contains material data. for fgms, read data from "fgm_node_values." for temperature-dependent properties, segmental data arrays contain the properties.

13. if q-values given by user, we compute domain integral right now. otherwise, we set up a loop to generate q-values for automatic domains and their computation.

14. user wants automatic construction of domains.

14a. get last ring at which output will be printed. domains are always generated starting at ring 1 but j-values may not be computed for every domain.

14b. allocate arrays needed to support construction/definition of the domains. for type 4, we need only a nodal bit map. for types 1-3, we need two sets of nodal bit maps. each set has 3 maps of length to record all structure nodes. element list stored in the common vector.

14c. set up to accumulate statistics for computed domain values.

14d. loop over all domains. construct definition of the domain (q-values, element list). call driver to actually calculate value for domain.

call ``diexp4.f``::

    c ***************************************************************
    c *                                                             *
    c * domain expand 4 - expand type 4 automatic domain            *
    c * domain expand 13 - expand type 1-3 automatic domain         *
    c *                                                             *
    c ***************************************************************

call ``dicmj.f``::

    c ***************************************************************
    c *                                                             *
    c * domain_compute - drive execution of element routine to      *
    c *                  compute j and i-integrals for a single     *
    c *                  domain                                     *
    c *                                                             *
    c ***************************************************************

14e. release allocatable arrays for automatic domains

15a. write j-integral and i-integral data to standard output

15b. write j-integral and i-integral data to packets

16. release arrays used for both user defined and automatic domains

