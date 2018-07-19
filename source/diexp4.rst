``diexp4.f``: Domain Integral EXPand
=====================================

Calling Tree
-------------

::

    c ***************************************************************
    c *                                                             *
    c *    diexp4.f                                                 *
    c *       -diexp4                                               *
    c *            -diadit                                          *
    c *            -dibmck                                          *
    c *       -diexp13                                              *
    c *            -diadit                                          *
    c *            -dibmck                                          *
    c *            -digete                                          *
    c *                                                             *
    c ***************************************************************

::

    c *******************************************************************
    c *                                                                 *
    c *   diadit -- add to bit map from specified item                  *
    c *                                                                 *
    c *   dibmck -- test for entry presence in a bit map                *
    c *                                                                 *
    c *   digete -- get element edge data                               *
    c *                                                                 *
    c *******************************************************************

``diexp4``: Domain Integral EXPand domain_type 4
-------------------------------------------------

::

    c ***************************************************************
    c *                                                             *
    c * domain expand 4 - expand type 4 automatic domain            *
    c *                                                             *
    c ***************************************************************


``diexp13``: Domain Integral EXPand domain_type 1-3
----------------------------------------------------

::

    call diexp13( ring, nonode, noelem, incmap, iprops, incid,
     &                    out, bits, q_new_map, q_old_map, q_map_len )
    c ***************************************************************
    c *                                                             *
    c * domain expand 13 - expand type 1-3 automatic domain         *
    c *                                                             *
    c ***************************************************************

convert list of coincident nodes at each corner node on the front to a bit map. there are 2 or three corner nodes. for 2 corner nodes we just leave one of the maps all zero which makes code logic simpler in later steps. set which positions in the list of front nodes are the corner nodes.

loop over all edges of this element. get the two structure nodes corresponding to the two corner nodes for the edge. classify the edge based on it's connectivity to nodes listed in the three node maps. build 3 logical flags for each corner node, marking it's appearance in the 3 node maps. based on connectivity of edge nodes on the 3 nodes lists, add a corner node to the lists.

::

    logic tree:
    0 = both corner nodes appear in the 3 node lists
    0 = neither corner node appears in the 3 node lists
    if corner node 1 only appears in the 3 lists,
        add corner node 2 to the node list in which corner
        node 1 appears.
    if corner node 2 only appears in the 3 lists,
        add corner node 1 to the node list in which corner
        node 2 appears.