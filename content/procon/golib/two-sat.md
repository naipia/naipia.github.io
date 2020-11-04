---
title: "2-SAT"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - graph
---

```go
type twosat struct {
	scc *scc
	ans []bool
	n   int
}

func newTwoSAT(n int) *twosat {
	return &twosat{
		newStronglyConnectedComponents(2 * n), make([]bool, n), n,
	}
}

func (ts *twosat) AddClause(i int, f bool, j int, g bool) {
	p, q, a, b := 2*i, 2*j, 0, 0
	if f {
		a = 1
	}
	if g {
		b = 1
	}
	ts.scc.AddEdge(p+a^1, q+b)
	ts.scc.AddEdge(q+b^1, p+a)
}

func (ts *twosat) Satisfiable() bool {
	ts.scc.Scc()
	cmp := ts.scc.cmp
	for i := 0; i < ts.n; i++ {
		if cmp[2*i] == cmp[2*i+1] {
			return false
		}
		ts.ans[i] = cmp[2*i] < cmp[2*i+1]
	}
	return true
}

func (ts *twosat) Answer() []bool {
	return ts.ans
}

type scc struct {
	g   [][]int
	rg  [][]int
	cmp []int
}

func newStronglyConnectedComponents(n int) *scc {
	return &scc{
		make([][]int, n), make([][]int, n), make([]int, n),
	}
}

func (s *scc) AddEdge(v, to int) {
	s.g[v] = append(s.g[v], to)
	s.rg[to] = append(s.rg[to], v)
}

func (s *scc) Scc() [][]int {
	n := len(s.g)
	vs := make([]int, 0, n)
	vis := make([]bool, n)

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
		s.cmp[v] = k
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
		t[s.cmp[i]] = append(t[s.cmp[i]], i)
	}

	return t
}

```
