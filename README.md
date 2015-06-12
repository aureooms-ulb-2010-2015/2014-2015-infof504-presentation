# Combinatorial Redundancy Detection

## Some background on output sensitive algorithms

An output sensitive algorithm is an algorithm whose complexity depends on the
size of the output. Thus, for a fixed instance size *n*, the same algorithm can
have different running times. A classical example of a problem solved by output
sensitive algorithms is the computation of the convex hull of a finite
set of points in *R^d*.

### A canonical output sensitive algorithm

Clarkson [1] gives an output sensitive algorithm to find the minima
set of a set of elements according to some partial order.
This algorithm uses at most *2nA* comparisons where *A* is the cardinality of
the minima set.


#### Pseudo Code

**input** *<* , *S*

  1. Let *M* be the empty set.
  2. While there are candidate elements left in *S*:
    1. Let *x* be one of the candidates and decide whether it
       is dominated by one of the minima elements in *M* using predicate *<*.
    2. If so, discard *x*.
    3. Otherwise, scan the candidates for a minimum according to *<*. If at this point the
       candidate set is not empty, at least one of its elements must be a
       minimum. We scan the candidate list to find this element.
      1. For each other candidate *y* than *x*:
        1. If *x < y*, discard *y* from *S*.
        2. Else, if *y < x*, we can discard *x* from *S* and let *y*
           be the new *x*.
        3. Otherwise, do nothing.

      2. Let *M <- M union {x}* and *S <- S \ {x}*.

  3. Output *M*.

#### Running-time Analysis

For step **2.i**, at most *n.A* comparisons are used since we compare each element
of *S* with each elements of the minima set which is of cardinality at most *A*
during the execution of the algorithm.

For step **2.iii**, for each executed loop we
obtain a new minimum and increase the size of the constructed minima set by
*1*, hence there are at most *A* loops execution, each of which loops over at
most *n* elements. Step **2.iii** uses thus at most *nA* comparisons.

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

### Clarkson's Algorithm

Clarkson [1] gives an algorithm that runs much faster when the number *s* of
nonredundant half-spaces is small relative to *n*.

We make several assumptions on the input:

  1. The polyhedron defined by *H* is full-dimensional.
  2. No equality is a positive multiple of another one.
  3. We are given a vertex *z* in the interior of the polyhedron defined by *H*.

These assumptions can be guaranteed using *d LP(d,n)* time. See [2] for details.

#### Pseudo Code

**input** *H*, *z*

  1. Let *M* be the empty set.
  2. While there are candidate elements left in *H*:
    1. Let *h* be one of the candidates and decide whether it
       is redundant with respect to the set of half-spaces *M*.
    2. If so, discard *h*.
    3. Otherwise, scan the candidates for a nonredundant half-space. If at this point the
       candidate set is not empty, at least one of its elements must be
       nonredundant. We scan the candidate list to find this element.

  3. Output *M*.

Note the similarity between this algorithm and the minima finding algorithm we
introduced earlier.

#### Running-time Analysis

For step **2.i**, we solve a linear program in *d* variables with at most
*s+1* constraints. In the case where *h* is nonredundant with respect to *M* we
obtain an optimal solution _x*_ lying on the boundary of *h*. There are *n*
such steps since after each execution of the main loop we either find
a nonredundant half-space or discard one.

For step **2.iii**, we use the following *rayshoot* procedure which given a set of
hyperplanes *H*, a vertex *z* and a direction *r* finds the first hyperplane
from *H* to meet the ray shot from *z* in direction *r*. Here we give a
simplified version of the algorithm that assumes a candidate solution is already
known, which is the case for us. In the description below, *h* denotes this
candidate solution and *w >= 0* is the scalar such that *z + wr* lies on *h*.

**input** *H*, *z*, *r*, *h*, *w*

  1. For each candidate *g* in *H*:

    1. Compute *l = eval( g , z ) / dot( g , r )*, where *eval* evaluates the
       linear expression *g = a_0 + a_1 x_1 + ... + a_n x_n* for *x = z* and
       where *dot(.,.)* denotes the dot product.

    2. if *l >= 0* and *l < w* let *l* be the new *w* and *g* the new *h*  ;

The complexity of the rayshoot algorithm is *O(nd)*. After each execution of
step **2.iii** we find a new nonredundant half-space and thus there are at most
*s* such steps.

The total comlexity of the algorithm is thus *O(n LP(d,s) + snd)*.

## References

  - [1] CLARKSON, Kenneth L. More output-sensitive geometric algorithms. In :
*Foundations of Computer Science, 1994 Proceedings., 35th Annual Symposium on.*
IEEE, 1994. p. 695-702.
  - [2] [Lecture: Polyhedral Computation, Spring 2015](http://www-oldurls.inf.ethz.ch/personal/fukudak/lect/pclect/notes2015/PolyComp2015.pdf)
