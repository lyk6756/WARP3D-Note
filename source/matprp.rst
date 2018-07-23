``matprp(300,500)``： Material Properties
===========================================

inmat.f

* ``main_data.matprp`` Declared as: REAL (300,500)
* ``main_data.imatprp`` Declared as: INTEGER (300,500)
* ``main_data.lmtprp`` Declared as: LOGICAL (300,500)
* ``main_data.smatprp`` Declared as: CHARACTER (len=24)(300,500)

::

    c                       assign default material values. the current
    c                       ordering of material values is:
    c
    c                  (*)  1  -- young's modulus
    c                  (*)  2  -- poisson's ratio
    c                       3  -- kinematic hardening ratio (beta)
    c                       4  -- tangent modulus for bilinear strain
    c                             hardening (et)
    c                       5  -- inviscid yield stress (sigma-o)
    c                  (*)  6  -- thermal expansion coefficient (isotropic)
    c                  (*)  7  -- mass density
    c                       8  -- linear elastic material flag
    c                       9  -- material model type
    c                              = 1 vectorized linear elastic
    c                                  and invisicid plasticity model.
    c                                  isotropic/kinematic hardening
    c                              = 2 nonlinear elastic with linear +
    c                                  power law. small strains only.
    c                                  rate independent
    c                              = 3 general gurson/mises model including
    c                                  nucleation, linear hardening,
    c                                  power-law hardening, matrix
    c                                  viscoplasticity
    c                              = 4 Cohesive zone models: linear elastic,
    c                                  bilinear, ramp, exponential_1 and
    c                                  exponential_2, ppr, cavitation
    c                              = 5 advanced cyclic plasticity model
    c                                  developed by kristine cochran
    c                              = 6 creep model
    c                              = 7 advanced mises model with hydrogen
    c                                  effects developed by yuiming liang
    c                              = 8 general UMAT for warp3d.
    c                              = 10 (matching back up with file #s)
    c                                   CP model by mark messner
    c                              = 11 interface damage model
    c                      10  -- viscoplastic m power
    c                      11  -- power-law hardening n power
    c                      12  -- viscoplastic reference strain rate
    c                      13  -- debug material model computations
    c                      14  -- initial porosity (f sub 0)
    c                      15  -- gurson model parameter q1
    c                      16  -- gurson model parameter q2
    c                      17  -- gurson model parameter q3
    c                      18  -- nucleation flag (true or false)
    c                      19  -- nucleation parameter sn
    c                      20  -- nucleation parameter en
    c                      21  -- nucleation parameter fn
    c                      22  -- allow material model to cut step due to
    c                      23  -- flag to allow crack growth element killing
    c                             excessive reversed plasticity
    c                      24  -- segmental stress-strain curve logical flag
    c                      25  -- flag to indicate anisotropic thermal
    c                             coefficients are defined
    c                      26-31  anisotropic thermal expansion coefficients
    c                      32  -- interface stiffness in the longitudinal direction
    c                      33  -- interface stiffness in the transverse direction
    c                      34  -- interface stiffness in the normal direction
    c                      35  -- critical normal stress of the interface
    c                      36  -- critical shear stress of the interface
    c                      37  -- shape parameter for bilinear and ramp
    c                      38  -- second ( additional) shape parameter for ramp
    c                      39  -- critical separation distance in sliding
    c                      40  -- critical separation distance in opening
    c                      41  -- equivalent critical separation distance
    c                      42  -- a ratio to determine the equivalent separation
    c                             under mixed mode loading ( =0 => mode I )
    c                      43  -- a flag for identifying the interface element
    c                      44  -- type of interface models
    c                             1 - linear elastic; 2- bilinear
    c                             3 - ramp; 4 - exponential_1; 5 - exponential_2
    c                      45  -- for segmental curve model, the segmental curve
    c                             set number
    c                  (*) 46  -- ductile material volume fracture for fgm
    c                      47  -- = 0 homogeneous cohesive material
    c                             = 1 functionally graded cohesive material
    c                      48  -- critical separation distance ductile (fgm)
    c                      49  -- critical separation distance brittle (fgm)
    c                      50  -- critical stress ductile (fgm)
    c                      51  -- critical stress brittle (fgm)
    c                      52  -- beta_ductile (fgm)
    c                      53  -- beta_brittle (fgm)
    c                      54  -- compression stiffness multiplier for
    c                             cohesive materials
    c                      55  -- start of props for cyclic plasticity model
    c                              see inmat_cyclic
    c                      70 -- start of props for yuemin liang's adv.
    c                             mises model that includes effects of
    c                             staturated hydrogen
    c                              70 - yl_1
    c                              71 - yl_2
    c                              72 - yl_3
    c                              73 - yl_4
    c                              74 - yl_5
    c                              75 - yl_6
    c                              76 - yl_7
    c                              77 - yl_8
    c                              78 - yl_9
    c                              79 - yl_10
    c
    c                      80-89  creep model
    c
    c                      90-- PPR cohesive model
    c                              90-98 currently used. see comments
    c                              in inmat_cohesive routine
    c                      100-- local tolerance for CP NR loop
    c                      101-- number of crystals at e. material point (or max n)
    c                      102-- angle convention (1=Kocks)
    c                      103-- angle type (1=degree, 2=radians)
    c                      104-- crystal input (1=single, 2=file)
    c                      105-- crystal number (for single)
    c                      106-- crystal offset (for list)
    c                      107-- orientation input (1=single, 2=file)
    c                      108-110-- psi, theta, phi (for single)
    c                      111-- orientation offset (for list)
    c                      112-- STRING crystal list (for offset/list)
    c                      113-- STRING orientation list (for offset/list)
    c
    c                      115-- macroscale material model number
    c                      116-118-- s vector
    c                      119-121-- l vector
    c                      122-125-- t vector
    c                      126-- l_s
    c                      127-- l_l
    c                      128-- l_t
    c                      129-- alpha_dmg
    c                      130-- nstacks (temp, should calculate from element sz)
    c                      131-- nfail ("")
    c                       132-- macro_sz
    c                       133-- cp_sz
    c
    c                      148-- link2 x-stiffness
    c                      149-- link2 y-stiffness
    c                      150-- link2 z-stiffness
    c
    c                      151-200 -- Abaqus compatible UMAT
    c                              151 - um_1
    c                              151 - um_2
    c                              ...
    c                              ...
    c                              200 - um_50
    c
    c                      201-230 cavity option for cohesive material
    c
    c                      the beta_fact is used to assist in construction
    c                      of planar models containing one layer of elements.
    c                      the stiffness and internal forces are mutiplied
    c                      by the beta_fact to simulate the effect of a
    c                      non-unit thickness.
    c
    c                  (*) some material values maybe specified as having the
    c                      value 'fgm' (a string). In such cases the user
    c                      supplied nodal values for the model are interpolated
    c                      at the element gauss points during execution.