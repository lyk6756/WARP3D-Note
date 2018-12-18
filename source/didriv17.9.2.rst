Version 17.9.2: ``didriv.f``: Domain Integral DRIVer
=============================================================

Description
------------

domain driver:
    drive the computation of j-integral values using the domain integral technique.

    or:

    drive the computation of mixed-mode stress intensity factors and t-stress using the interaction integral.

``didriv.f`` Declaration Tree
-------------------------------

.. image:: https://qxrkig.dm.files.1drv.com/y4mGc5CkNNVyvkUmXKptXEU715iIf-zyrOX2cXWrquzNqxyyBdkkbPjJyV5jjMG3XMgwumFD0_-EPi99qGKCqNddRYY4BSJi25m91GS7XqjZPA5RfqMHqSmt1IXak25JwuJrgDtkrw4ED3eMbjPP7yWwCCKsbsRtVyzTBZYR0yDoD7sPvtNFpET3DwvaIeEamBR4_L2ZBpk3wuU4TIKat0jqA?width=818&height=1747&cropmode=none

``didriv.f`` Calls Map
------------------------

.. image:: https://q3rkig.dm.files.1drv.com/y4mZDtm6yrCFCf7IRj6M8ZDMGC1kYhfYFqgg5AtWVCGmZvlVypVx2pY2MebEvYgzxWIn8nClmUcY0SanAZ_KJ8TmT1KdQrOPpzeaKyWphKam2ok8KSWPHapqYrwi_IaQC4sadhimAHSB1NrXAOXmfm6fztC2rcrU80waoNCJD3tkqWyHc0CqP-ix5uChEv-geIyFVx6ROM_TjShNG4OzT_V5w?width=1741&height=2343&cropmode=none

Procedure
----------

1. check validity of domain before starting computations.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在开始计算前检查积分域

2. perform exhaustive check on consistency of 1) number of front nodes, 2) front interpolation order, and 3) domain type.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对一致性进行彻底检查：1）裂尖节点数，2）裂尖插值阶数，3）积分域类型。

call ``dickdm``::

    c ***************************************************************
    c *                                                             *
    c * domain_check  - exhaustive testing of domain defintiion for *
    c *                 consistency                                 *
    c *                                                             *
    c ***************************************************************

3. allocate space for a q-value at each structure node. allocate space for expanded lists of coincident front nodes at each front location.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

为q_values（q值函数）和expanded_front_nodes（裂尖重合节点）分配内存

4. output header information for domain
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

输出积分域的头信息

call ``diheadr``::

    c ***************************************************************
    c *                                                             *
    c * domain_header - output info at start of domain computations *
    c *                                                             *
    c ***************************************************************

5. when q-values are given by the user, expand the compressed list of q-values into a list of length number of structure nodes.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

当用户给出q值时，将压缩的q值列表展开为长度为结构节点数的列表。

6. set up the domain. for automatic domains, set q-values at front nodes. then find coincident front nodes and set their q-values.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

设置q值函数。对于自动q值积分域，在裂尖节点设置q值。然后找到重合的裂尖节点并设置它们的q值。

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

7a. create vector with list of elements on the crack front for this domain
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

创建积分域中位于裂尖的单元列表的数组

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

7b. use crack front element to set if J computations will use small or large strain formulation. all elements in domain must match be the same formulation.

依据裂尖单元设置计算J积分使用小应变或大应变形式。积分域中的所有单元必须使用相同的变形形式。

8. at point on front where integral is being computed, build the global->crack rotation matrix. gather coordinates and displacements of crack-front nodes, and rotate them to local crack-front system.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在将要计算积分的裂尖点处，建立全局至裂尖旋转矩阵。收集裂尖节点的坐标和位移，并将它们旋转到裂尖局部坐标系。

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

计算域起源节点处的应变e33。这是用于使用相互作用积分的T-应力计算。 仅线性弹性分析

call ``di_calc_e33``::

    c *******************************************************************
    c *                                                                 *
    c *   calculate strain e33 at domain origin for T-stress calcs.     *
    c *   calculate strain e33 as the difference between the            *
    c *   deformed and undeformed crack-front lengths delta_L / L       *
    c *                                                                 *
    c *******************************************************************

8c. calculate properties of a curve passing through the front nodes. these will be used to compute distance 'r' from integration points to a curved crack front.

计算通过前节点的曲线的属性。这将用于计算从积分点到弯曲裂纹前沿的距离‘r’。交互积分的线弹性分析

call ``di_calc_curvature`` from ::

    c *******************************************************************
    c *                                                                 *
    c *   calculate coefficients of curve described by crack front      *
    c *   nodes.                                                        *
    c *                                                                 *
    c *******************************************************************

9. compute area under the q-function over that part of crack front for this domain. the area must be >0 else fatal error in domain (user forgot to set q-values on front nodes)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

计算在该积分域中裂纹前沿的q函数下的面积。面积必须大于0

call ``difrar.f``::

    c **********************************************************************
    c *                                                                    *
    c * di_front_q_area - compute area under q-function along front for    *
    c *                   this domain                                      *
    c *                                                                    *
    c **********************************************************************

10. formerly set logical flags to indicate if the nodal velocities and accelerations are all zero for this load step.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**no longer used**

以前设置逻辑标志以指示此加载步骤的节点速度和加速度是否都为零。

11.
~~~~~

Need to do this when temperatures have been specified in model. Possibly leading to computation of J_6

Not needed if the user has input initial stresses or set an initial state for J-processing to accommodate prior thermo-mechanical processing. In such cases those effects, FGMs, thermal, ... will be included thru J_7, J_8

Build the node average value of thermal expansion coefficient. for temperature-dependent material properties, also build the node average value of young's modulus and poisson's ratio. For temperature-independent material properties, values of e and nu are obtained within dicmj.f

nodal properties are needed for domain integral computations to compute spatial derivatives within the domain.

在模型中指定温度时需要执行此操作。可能导致J_6的计算

如果用户输入初始应力或设置J处理的初始状态以适应先前的热机械处理，则不执行此操作。在这种情况下，这些效果（FGM，热，等）将包括在J_7，J_8中

建立热膨胀系数的节点平均值。对于温度依赖的材料特性，还建立杨氏模量和泊松比的节点平均值。对于与温度无关的材料属性，e和nu的值在dicmj.f中获得。

区域积分的计算需要节点属性来计算域内的空间导数。

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

12.
~~~~~

Build the nodal averages of strain energy density (stress work density) and strains. These terms are used to calculate the derivative of the strain energy density, which appears in the domain integral when material properties vary spatially (e.g. fgms). The nodal values calculated in di_fgm_setup will be used to compute their spatial derivatives at integration points.

Needed when FGM nodal properties are actually used for elements in model, when the user has defined an initial state, or temperature dependent stress-strain curves are used (effectively makes the material an FGM), or input initial stresses.

Needed for I integrals with temperatures in model.

Even if not needed, the code builds small, dummy arrays to satisfy checks.

Build the nodal averages of strain energy density (W) and nodal values of displacement gradient (3x3) at t_n relative to coordinates at t=0. These terms are used to calculate the derivative of W wrt crack local X and derivatives of the gradients wrt crack local X -- both at integration points for J(7) and J(8).

当1）FGM节点属性实际用于模型中的单元，2）用户定义了初始状态，3）使用温度相关的应力-应变曲线（有效地使材料成为FGM），4）输入初始应力时，需要需要执行此操作。

模型中有温度时需要执行此操作。即使不需要执行，代码也会构建小的虚拟数组以满足检查要求。

在t_n处相对于坐标t=0处建立应变能密度（W）的节点平均值和位移梯度（3×3）的节点值。这些项用于计算应变能密度相对于裂尖局部坐标X的导数和位移梯度相对于裂尖局部坐标X的导数。这两个量在积分点处的值都将用在J（7）和J（8）中。

call ``di_setup_J7_J8``::

    c **********************************************************************
    c *                                                                    *
    c * allocate data structures and drive computations                    *
    c * to enable subsequent computations of J(7) and J(8) terms.          *
    c *                                                                    *
    c * if computations not actually required, create small, dummy arrays  *
    c * to simplify checking                                               *                                                                           *
    c *                                                                    *
    c * get a stress work value (W) at all model nodes by extrapoaltion    *
    c * from element integration point values, then node averaging         *
    c *                                                                    *
    c * also get the displacement gradiaents partial(u_i/x_j) (3x3)        *
    c * at model nodes by extrapolation from element integration points    *
    c *                                                                    *
    c **********************************************************************

12b. at point on front where integral is being computed, collect young's modulus and poisson's ratio. this assumes that all elements connected to this crack-front node have identical, homogeneous material properties, or that fgm material properties have been assigned to the model. for homogeneous material, "props" contains  material data. for fgms, read data from "fgm_node_values." for temperature-dependent properties, segmental data arrays contain the properties.

提取在计算积分的裂尖点处的杨氏模量和泊松比。这里假设连接到此裂尖节点的所有单元具有相同的均匀材料属性，或者已将梯度材料属性分配给模型。对于均匀材料，变量“props”储存了材料数据。对于梯度材料，从变量“fgm_node_values”读取数据。对于与温度相关的材料属性，分段数据数组包含属性。

12c. at point on front where integral is being computed, props(7,elemno) and props(8,elemno) may be zero if the element has been killed. in this case, use the first nonzero values of e and nu.

在计算积分的裂尖点上，如果单元被杀死，则props（7，elemno）和props（8，elemno）可能为零。 在这种情况下，使用第一个e和nu的非零值。

12d. output info for domain about temperature at crack front and alpha values. Also E, nu at front

输出积分域关于裂尖的温度，热膨胀系数，杨氏模量和泊松比的信息。

13a. init various tracking values for for J and I
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

初始化用于J积分和I积分的数组

13b. separate drivers for comput values on 1 domain for user-defined q-values, and for defining-computing over automatic domains

分离在一个积分域上使用用户定义的q值进行J、I积分，或在一个积分域上使用自动q值进行J、I积分

14. user wants automatic construction of domains.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

15a. write j-integral and i-integral data to standard output
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将j积分和i积分数据写入标准输出

15b. write j-integral and i-integral data to packets
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将j积分和i积分数据写入数据包

16. release arrays used for both user defined and automatic domains. call routines to get releases on other than rank 0 for MPI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

释放用于用户定义积分域和自动域的数组
