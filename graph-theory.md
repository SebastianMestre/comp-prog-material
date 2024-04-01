
graph theory

dfs and bfs

void search(int s) {
	bag q; // stack<int> or queue<int>
	q.push(s);
	while (not q.empty()) {
		int u = q.take(); // top() or front(), respectively
		q.pop();
		if (visited[u]) continue;
		visited[u] = true;
		for (int v : G[u]) {
			q.push(v);
		}
	}
}

belman-ford and dijkstra

floyd-warshal

kruskal
