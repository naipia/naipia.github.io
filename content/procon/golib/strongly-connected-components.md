---
title: "強連結成分分解"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - graph
---

```go
type scc struct {
	g  [][]int
	rg [][]int
}

func newStronglyConnectedComponents(n int) *scc {
	return &scc{make([][]int, n), make([][]int, n)}
}

func (s *scc) AddEdge(v, to int) {
	s.g[v] = append(s.g[v], to)
	s.rg[to] = append(s.rg[to], v)
}

func (s *scc) Scc() [][]int {
	n := len(s.g)
	vs := make([]int, 0, n)
	vis := make([]bool, n)
	cmp := make([]int, n)

	var dfs func(v int)
	dfs = func(v int) {
		vis[v] = true
		for _, to := range s.g[v] {
			if !vis[to] {
				dfs(to)
			}
		}
		vs = append(vs, v)
	}

	var rdfs func(v, k int)
	rdfs = func(v, k int) {
		vis[v] = true
		cmp[v] = k
		for _, to := range s.rg[v] {
			if !vis[to] {
				rdfs(to, k)
			}
		}
	}

	for i := 0; i < n; i++ {
		if !vis[i] {
			dfs(i)
		}
	}

	for i := 0; i < n; i++ {
		vis[i] = false
	}

	k := 0
	for i := len(vs) - 1; i >= 0; i-- {
		if !vis[vs[i]] {
			rdfs(vs[i], k)
			k++
		}
	}

	t := make([][]int, k)
	for i := 0; i < n; i++ {
		t[cmp[i]] = append(t[cmp[i]], i)
	}

	return t
}

```
