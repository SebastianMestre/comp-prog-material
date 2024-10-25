# P, NP, NP-Completeness

- P is the set of problems that can be solved in polynomial time (e.g. sorting, maximum matching on a graph)
- NP is the set of problems for which solutions can be checked in polynomial time (e.g. find a Hamiltonian cycle, plus all problems in P)
- A problem is NP-Hard if all the problems in NP can be *reduced* (more on that later) to it in polynomial time
- The subset of NP that is NP-Hard is called NP-Complete

# CNF-SAT

The satisfiability problem (SAT): Given a propositional formula find an assignment of the variables that make the formula true, or report that none exists.

For example (add example here)

CNF-SAT is a restricted version of SAT where propositions must be in conjunctive normal form.

```math
(\neg a \lor b) \land (a \lor b \lor c) \land (a \lor \neg c)
```

# Sudoku

Sudoku is a puzzle played on a 9x9 grid. The grid initially has some digits from one to nine written on it.
In order to solve the puzzle, every cell in the grid must be filled with a digit from one to nine. Furthermore,
no two cells in the same row, column or 3x3 block may contain the same digit.

We can convert any sudoku instance to a SAT instance.

To do so, we will employ 729 propositional variables with the following meaning:

```math
v_{ijk} = \texttt{The cell (i,j) has value k}
```

The constraint that same-row cells cannot have the same value expands into a list of clauses like the following:

```math
\neg ( v_{119} \land v_{129} )
```

Meaning cell (1,1) and cell (1,2) cannot simultaneously have the value 9

To make it compatible with CNF-SAT, it can be changed into a disjunction by De Morgan's law:

```math
( \neg v_{119} \lor \neg v_{129} )
```

The same-column and same-block constraints can be translated in a similar way.

We also need to specify that every cell must have *some* value to avoid the trivial solution of setting all variables to false.

```math
(v_{111} \lor v_{112} \lor v_{113} \lor v_{114} \lor v_{115} \lor v_{116} \lor v_{117} \lor v_{118} \lor v_{119})
```

This is already compatible with CNF-SAT.

Finally, we add constraints to fix any given digits

```math
v137
```

Once this conversion is done, we can feed this SAT instance to any solver and get a solution to our sudoku puzzle.

In this sense, sudoku can be *reduced* to SAT.

In fact, any NP problem can be converted to SAT.

As a proof sketch, consider that any problem in NP can be checked in polynomial time by a program. It is not hard to
imagine that, since programs run on digital circuits, we can convert that program into a digital circuit of polynomial size.
Then, we could convert that circuit into a propositional formula of polynomial size (a SAT instance). Finally, we can use
a SAT solver to figure out what input makes the checker answer "true", thus solving the problem.

# NP-Complete, revisited

It is widely believed that the class P and the class NP-Complete are disjoint, meaning that NP-Complete problems cannot
be solved in polynomial time.

While this is true, we can still do better than a naive algorithm on NP-Complete problems in three different ways:

- Use standard techniques to find algorithms with somewhat lower complexity
- Use heuristics to get acceptable performance on common cases
- Develop specialized algorithms for special cases

# Case study: Travelling Salesman Problem

The naive algorithm tries all permutations, runs in O(N\*N!)

Improvement: We can use dynamic programming over subsets of nodes to get an O(N^2\*2^N) algorithm

# Case study: Hamiltonian cycle

The naive algorithm performs a depth-first backtracking search that tries all paths, runs in O(N\*N!)

A simple yet effective heuristic is: when the DFS gets stuck and one of the neighbors is a previously
visited node, we re-splice the path in a different way, allowing us to get out of the stuck state.
Still O(N\*N!), but often fast in practice.

# Case study: Maximum clique

The naive algorithm tries all subsets of nodes, perhaps with backtracking, runs in O(N^2 \* 2^N)

Let us focus on planar graphs. 5-cliques are non-planar, so we will only find up to 4-cliques.
With this, we have an O(N^4) algorithm, but it can be improved.

In a planar graph, there is
always a node with 5 or fewer neighbors. We can try all subsets of neighbors of that node in
constant time (2^5 is still a constant), then delete that node. Since the resulting graph is
still planar, we can repeat this procedure until nodes are exhausted. With clever implementation,
it can be done in O(N) time.
