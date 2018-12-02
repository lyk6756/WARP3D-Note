``distup.f``: Domain Integral SeT UP
======================================

Description
------------

call ``distup( c, crdmap, nonode, debug_driver, out )``

set_up_domain:

    set up the domain for processing find coincident front nodes and set their q-values

Procedure
----------

1 determine if the user has specified the crack front nodes using node sets. this is done when the model is not collapsed at the ct. For a user defined crack tip, load expanded_front_nodes with node_set specified by the user. check to be sure that each front position has the same number of crack tip nodes.

确定用户是否使用节点集指定了裂尖节点。当模型未在裂尖处塌缩时完成此操作。对于用户定义的裂尖，使用用户指定的节点集加载expanded_front_nodes数组。检查以确保每个裂尖位置具有相同数量的裂尖节点

2. for automatic domain definitions, set the q-values at user specified front nodes based on the domain type and the user specified front interpolation order.

确定用户是否使用节点集指定了裂尖节点。当模型未在裂尖处塌缩时完成此操作。对于用户定义的裂尖，使用用户指定的节点集加载expanded_front_nodes数组。检查以确保每个裂尖位置具有相同数量的裂尖节点

3. if linear interpolation of q-values over the domain is on, we also need to interpolate q-values along front for quadratic elements.

对于自动积分域定义，根据区域类型和用户指定的插值阶数在用户指定的裂尖点处设置q值。

4. (skip this section when the user has defined the ct.) look at first 1 or 2 nodes specified to be on the crack front. compute a distance to be used for locating all other nodes coincident with each specified crack front node. set the "box" size for proximity checking as a fraction of the distance measure.

4. loop over each specified front node. locate all other nodes in model that are coincident with the front node. (use the box test). set the q-value of each coincident node to be same as user specified value for the specified front node. dump verification list if user requested it.
