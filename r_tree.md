### R-TREE

-Non-leaf node: pointer 
-Leaf node: entry 
-Number of entry each node  M / 2 <= x <= M  (M max node) 
-Each index record (I, tuple identifier) => Smallest rectangle spatially contains the n-dimensional data object respresented by the indicated node
- Height <= log_m(N) - 1 
-Each entry (I, child_pointer)
-When a node is full => split node 
-When a node contains less than M / 2 => combine node

**Searching and Updating** 
-T is root, search for rectangle S 
-S1: if T not  leaf, check entry E overlaps S, point to that entry 
-S2: if T is leaf, if T overlaps S => return T.


**Inserting**
-Insert E into R-Tree.
-I1: ChooseLeaf L to insert E. 
-I2: If L has room for another entry, insert E. Else Splitnode.
-I3: Invoke AdjustTre on L, passing LL if splitnode.

**ChooseLeaf**
-Select node to insert new index entry E.
-CL1: N - root
-CL2: if N is a leaf, return N.
-CL3: If N not root => choose N that overlap most of E. 
      If more than 1 => choose N with smallest area.
-CL4: repeat CL2

**AdjustTree** 
From a leaf L to the root 
-AT1: Set N=L if L was split previously, 
set NN to be the resulting second node
-AT2: If N is the root, stop
-AT3: Adjust E(N) with P is parent node so it tightly encloses 
all entry rectangles in N
-AT4: [Propagate node split upward] if N has a partner NN resulting
from an earlier split, create new entry E(NN) with E(NN)P point to NN
and E(NN)I enclosing all rectangles in NN.



- 
