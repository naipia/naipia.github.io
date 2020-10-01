---
title: "最小共通祖先"
math: false
categories:
  - 競技プログラミング
tags:
  - Go言語
  - library
  - graph
---

```go
type lca struct {
	dep []int
	par [][]int
}

func newLowestCommonAncestor(g [][]int, r int) *lca {
	n := len(g)
	k := 1
	for 1<<uint(k) < n {
		k++
	}
	lca := lca{make([]int, n), make([][]int, k)}
	for i := 0; i < k; i++ {
		lca.par[i] = make([]int, n)
	}
	var dfs func(v, p, d int)
	dfs = func(v, p, d int) {
		lca.par[0][v] = p
		lca.dep[v] = d
		d++
		for _, to := range g[v] {
			if to == p {
				continue
			}
			dfs(to, v, d)
		}
	}
	dfs(r, -1, 0)
	for i := 0; i < len(lca.par)-1; i++ {
		for j := 0; j < n; j++ {
			if lca.par[i][j] == -1 {
				lca.par[i+1][j] = -1
			} else {
				lca.par[i+1][j] = lca.par[i][lca.par[i][j]]
			}
		}
	}

	return &lca
}

func (a lca) Query(u, v int) int {
	if a.dep[u] > a.dep[v] {
		u, v = v, u
	}
	for i := len(a.par) - 1; i >= 0; i-- {
		if (a.dep[v]-a.dep[u])>>uint(i)&1 == 1 {
			v = a.par[i][v]
		}
	}
	if u == v {
		return u
	}

	for i := len(a.par) - 1; i >= 0; i-- {
		if a.par[i][u] != a.par[i][v] {
			u = a.par[i][u]
			v = a.par[i][v]
		}
	}
	return a.par[0][u]
}

```
