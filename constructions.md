One classic idea or "meta-technique" for atacking problems is thinking constructively.

Many mathematical objects have simple constructions, and thinking about those often reveals hidden properties about whatever problem we are thinking about.

For example, an ellipse is defined by a set of points were the sum of distance to the two focal points of the ellipse is constant.

Not all definitions map directly to a nice construction, but the object may still have a nice construction.

EXAMPLE: given two natural numbers x and y, their greatest common divisor is the greatest natural number that is a divisor of both of them. This definition is useful, but not really what we want for problem-solving.

Instead, we may think of the euclidean algorithm.

    While x and y both non-zero, substract the lesser one from the greater one.
    After one becomes zero, then the non-zero value that remains is equal to the greatest common divisor of the original values.

This is a very nice construction and it often shows up in problems. (if something similar to this shows up in the problem, then that's a hint that the problem is related to GCD)

PROBLEM: https://codeforces.com/contest/1764/problem/B

EXAMPLE: given a simple, undirected graph with weights on the edges, a minimum spanning tree is a spanning tree whose sum of weights is minimal

The nice construction here is given by Kruskal's algorithm:

    Given a graph G = (V, E), create a new graph G' = (V, {})
	For each edge in G, in increasing order of weight, if adding it to G' doesn't create a cycle, then add it.
	After this, G' is a minimum spanning tree of G.

PROBLEM: Given a graph, find which edges belong to no/some/all minimum spanning trees. (TODO: find judge)
