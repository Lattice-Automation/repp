# decvec

declarative vector design

## Steps

1.  Filter out fragments that won't be in assemblies beneath the upper-fragment count limit

2.  Run a DP algo on the nodes to find each node's cost from the end

    2.1 Also, based on the cost estimate of #2, set the index of the next-node using its index
    This will be used later for determining where to create synthetic fragments

3.  Sort all "starting-nodes" by their estimated total assembly cost (low -> high)

4.  Traverse the cheapest assembly and "fill in the node":

    4.1 Create primers if it's going to be PCR'ed, create a synthetic fragment otherwise

    4.2 Fail out if:

         4.2.1 The primers have a very high primer3 penalty score OR
               The primers have off-target's in their source vector OR
               The node has an inverted repeat in its junction OR
               The synthetic fragment will be dificult to synthesize

               4.2.1.1 Remove fragment from tree and repeat #2

        4.2.2 The node has a duplicate end region with another fragment in the assembly

                4.2.2.1 Move to parent node and try the next cheapest path

5.  If there's more nodes in the traversed-filled node than the upper-limit:

    5.1 Try to merge fragments into synthetic vectors

        5.1.1 Recheck the assembly cost to see if it's still the cheapest

        5.1.2 If still cheapest, return the assembly

        5.1.3 If it's more expensive, move to next cheapest from #3 and repeat

## List of things that need to be considered when creating building fragments

1.  Don't create primers for a fragment if they have off-targets in the parent fragment

2.  Don't create primers that have an excessive primer3 penalty

3.  Don't create synthetic fragments that have high synthesis complexities

4.  Don't create fragments that have off-target end-homology

## Caveats

Addgene verified sequence information doesn't include all the of the vector sequence for some of the vectors. Therefore there might be off-targets that are invisible
