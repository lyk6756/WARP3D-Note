``props(mxelpr,mxel)``： Element Properties
============================================

elprp.f

* ``global_data.props`` Declared as: REAL (mxelpr,mxel)
* ``global_data.iprops`` Declared as: INTEGER (mxelpr,mxel)
* ``global_data.lprops`` Declared as: LOGICAL (mxelpr,mxel)

:math:`\begin{array}{rll}
\hline
\textrm{Parameter} & \textrm{Type} & \textrm{Description} \\
\hline
1   & \textrm{INTEGER}  & \textrm{element type} \\
2   & \textrm{INTEGER}  & \textrm{amount of nodes in an element} \\
3   & \textrm{INTEGER}  & = 24 \times \textrm{two16} + 6 \\
4   & \textrm{INTEGER}  & \textrm{number dof each element node} \\
5   & \textrm{INTEGER}  & \textrm{integration order} \\
6   & \textrm{INTEGER}  & \textrm{amount of integration points in an element} \\
7   &                   & \textrm{young's modulus} \\
8   &                   & \textrm{poisson's ratio} \\
9   &                   & \textrm{thermal expansion coeff.} \\
&                   & \alpha \textrm{for isotropic,} \alpha_x \textrm{for anisotropic} \\
10  &                   & \textrm{mass density} \\
11  & \textrm{INTEGER}  & = 6 \\
12  & \textrm{INTEGER}  & \textrm{output location} \\
13  &                   & \textrm{thermal expansion coeff.} \alpha_y \textrm{for anisotropic} \\
14  &                   & \textrm{kinematic hardening parameter(beta)} \\
15  &                   & \textrm{h-prime for linear hardening} \\
16  & \textrm{LOGICAL}  & \textrm{output format for output} \\
&                       & \textrm{the default value is .true. for short.} \\
17  & \textrm{LOGICAL}  & \textrm{request for linear material formulation} \\
18  & \textrm{LOGICAL}  & \textrm{geometric nonlinearity flag} \\
&                       & \textrm{the default value is .true. for on} \\
19  & \textrm{LOGICAL}  & \textrm{bbar flag} \\
&                       & \textrm{the default value is .false. for off} \\
20  &                   & \textrm{viscopls m-power} \\
21  &                   & \textrm{power-law hardening n-power or} \\
&                       & \textrm{set number for segmental stress-strain curves} \\
22  &                   & \textrm{viscoplastic ref strain rate} \\
&                       & \textrm{(the user inputs D which the inverse of eta,} \\
&                       & \textrm{here we change D to eta before passing the} \\
&                       & \textrm{value to props(22,elem) )} \\
23  &                   & \textrm{uniaxial yield stress (sig-0)} \\
24  &                   & \textrm{bit 1:  request for debug of material computations} \\
&                       & \textrm{bit 2:  allow material model to cutback adaptive} \\
&                       & \textrm{solution due to reversed plasticity} \\
&                       & \textrm{bit 3:  set of segmental stress-strain} \\
&                       & \textrm{curves specified for element's material} \\
25  & \textrm{INTEGER}  & \textrm{material model type} \\
26  &                   & \textrm{initial porosity for gurson model} \\
27  &                   & q_1 \textrm{for gurson model} \\
28  &                   & q_2 \textrm{for gurson model} \\
29  &                   & q_3 \textrm{for gurson model} \\
30  & \textrm{INTEGER}  & \textrm{bit 1:  logical nucleation flag for gurson model} \\
&                       & \textrm{bit 2:  flag if element is killable} \\
31  &                   & \textrm{s_n parameter for gurson model} \\
32  &                   & \textrm{eps_n parameter for gurson model} \\
33  &                   & \textrm{f_n parameter for gurson model} \\
34  &                   & \textrm{thermal expansion coeff.} \alpha_z \textrm{for anisotropic} \\
35  &                   & \textrm{thermal expansion coeff.} \alpha_{xy} \textrm{for anisotropic} \\
36  &                   & \textrm{thermal expansion coeff.} \alpha_{yz} \textrm{for anisotropic} \\
37  &                   & \textrm{thermal expansion coeff.} \alpha_{xz} \textrm{for anisotropic} \\
38  & \textrm{INTEGER}  & \textrm{material number during input} \\
\end{array}`

Catalogue of Element Numbers (types) in WARP3D
------------------------------------------------

:math:`\begin{array}{rll}
\hline
\textrm{Type} & \textrm{Name} & \textrm{Description} \\
\hline
1  & \textrm{q3disop}   & \textrm{20 node hex} \\
2  & \textrm{l3disop}   & \textrm{8 nodes hex} \\
3  & \textrm{ts12isop}  & \textrm{12 node hex transition} \\
4  & \textrm{ts15isop}  & \textrm{15 node hex transition} \\
5  & \textrm{ts9isop}   & \textrm{9 node hex transition} \\
6  & \textrm{tet10}     & \textrm{10 node tetrahedron} \\
7  & \textrm{wedge15}   & \textrm{15 node wedge element} \\
8  & \textrm{tri6}      & \textrm{6 node 2-D triangle} \\
9  & \textrm{quad8}     & \textrm{8 node 2-D quadralateral} \\
10 & \textrm{axiquad8}  & \textrm{8 node axisymmetric quadralateral} \\
11 & \textrm{axitri6}   & \textrm{6 node triangular, axisymmetric} \\
12 & \textrm{inter_8}   & \textrm{8 node quadralateral interface} \\
13 & \textrm{tet_4}     & \textrm{4 node tetrahedron} \\
14 & \textrm{trint6}    & \textrm{6 node triangular interface} \\
15 & \textrm{trint12}   & \textrm{12 node triangular interface} \\
16-17 & \textrm{***}    & \textrm{reserved to use in obtaining integration} \\
&                       & \textrm{points, derivatives, shape functions} \\
18 & \textrm{bar2}      & \textrm{2 node bar} \\
19 & \textrm{link2}     & \textrm{2 node link element} \\
\hline
\end{array}`
