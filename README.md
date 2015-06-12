# Combinatorial Redundancy Detection

## Some background on output sensitive algorithms

An output sensitive algorithm is an algorithm whose complexity depends on the
size of the output. Thus, for a fixed instance size *n*, the same algorithm can
have different running time. A classical example of a problem for which output
sensitive algorithms exists is the computation of the convex hull of a finite
set of points in *R^d*.

## A canonical output sensitive algorithm

Clarkson [1] gives an output sensitive algorithm to find the minima
set of a set of elements according to some partial order.

This algorithm uses at most *2nA* comparisons where *A* is the cardinality of
the minima set.


### Pseudo Code

**input** *<* , *S*

  1. Let *M* be the empty set.
  2. While there are candidate elements left in *S*:
    1. Let *x* be one of the candidates and decide whether it should
       be discarded because it
       is dominated by one of the minima elements in *M*.
    2. If so, discard *x*.
    3. Otherwise, scan the candidates for a minimum. If at this point the
       candidate set is not empty, at least one of its elements must be a
       minimum. We scan the candidate list to find such a minimum.
      1. For each other candidate *y* than *x*:
        1. If *x* precedes *y*, discard *y* from *S*.
        2. Else, if *y* precedes *x*, we can discard *x* from *S* and let *y*
           be the new *x*.
        3. Otherwise, do nothing.

      2. Let *M <- M union {x}* and *S <- S \ {x}*.

  3. Output *M*.

### Running-time Analysis

For step **2.1**, at most *n.A* comparisons are used since we compare each element
of *S* with each elements of the minima set which is of cardinality at most *A*
during the execution of the algorithm.

For step **2.3**, for each executed loop we
obtain a new minimum and increase the size of the constructed minima set by
*1*, hence there are at most *A* loops execution, each of which loops over at
most *n* elements. Step **2.3** uses thus at most *nA* comparisons.

The algorithm can be implemented so that it runs in place. The running time is
dominated by the comparison time and thus the
complexity of this algorihtm is *O(nA)*.


## Combinatorial Redundancy Detection

We want to solve the following problem:

> Given a set *H* of *n* half-spaces in *R^d* we want to filter out all half-spaces
*h* in *H* such that the intersection of all half-spaces in *H* does not change
if we remove *h* from *H*.

### Naive Algorithm

A simple way of solving the above problem is to solve *n* linear programs.
For each *h* we consider the set of linear constraints defined by the half-spaces in
*H \ h* and solve a linear program in *d* variables with these constraints and
with an objective function orthogonal to the direction of half-space *h*. If
the objective value of the optimal solution of this linear program
is different from the objective value of the optimal solution of the same
linear program with the addition of a new linear constraint defined by half-space
*h* then *h* is nonredundant. Otherwise, *h* is redundant.

Let *LP(d,n)* denote the complexity of solving a linear program in *d*
variables with *n* constraints, then the complexity of this algorithm is
*n LP(d,n)*.

### Clarkson Algorithm

## References

  [1] CLARKSON, Kenneth L. More output-sensitive geometric algorithms. In :
*Foundations of Computer Science, 1994 Proceedings., 35th Annual Symposium on.*
IEEE, 1994. p. 695-702.
