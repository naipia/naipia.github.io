---
title: "最大流"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - graph
---

```go
type edge struct{ from, to, cap, flow int }
type _edge struct{ to, cap, rev int }
type maxFlow struct {
	g    [][]_edge
	es   [][2]int
	lv   []int
	iter []int
}

func newMaxFlow(n int) *maxFlow {
	return &maxFlow{make([][]_edge, n), [][2]int{}, make([]int, n), make([]int, n)}
}

func (mf *maxFlow) AddEdge(from, to, cap int) {
	mf.es = append(mf.es, [2]int{from, len(mf.g[from])})
	mf.g[from] = append(mf.g[from], _edge{to, cap, len(mf.g[to])})
	mf.g[to] = append(mf.g[to], _edge{from, 0, len(mf.g[from]) - 1})
}

func (mf *maxFlow) Edge(i int) edge {
	v := mf.es[i]
	e := mf.g[v[0]][v[1]]
	r := mf.g[e.to][e.rev]
	return edge{v[0], e.to, e.cap + r.cap, r.cap}
}

func (mf *maxFlow) Edges() []edge {
	es := []edge{}
	for i := 0; i < len(mf.es); i++ {
		es = append(es, mf.Edge(i))
	}
	return es
}

func (mf *maxFlow) bfs(s int) {
	for i := 0; i < len(mf.g); i++ {
		mf.lv[i] = -1
	}
	mf.lv[s] = 0
	q := []int{s}
	for len(q) != 0 {
		v := q[0]
		q = q[1:]
		for _, e := range mf.g[v] {
			if e.cap == 0 || mf.lv[e.to] >= 0 {
				continue
			}
			mf.lv[e.to] = mf.lv[v] + 1
			q = append(q, e.to)
		}
	}
}

func (mf *maxFlow) dfs(v, t, f int) int {
	if v == t {
		return f
	}

	for i := &mf.iter[v]; *i < len(mf.g[v]); *i++ {
		e := &mf.g[v][*i]
		if e.cap == 0 || mf.lv[v] >= mf.lv[e.to] {
			continue
		}
		mi := f
		if mi > e.cap {
			mi = e.cap
		}
		if d := mf.dfs(e.to, t, mi); d > 0 {
			e.cap -= d
			mf.g[e.to][e.rev].cap += d
			return d
		}
	}
	return 0
}

func (mf *maxFlow) Flow(s, t, inf int) int {
	flow := 0
	for {
		mf.bfs(s)
		if mf.lv[t] < 0 {
			return flow
		}
		for i := 0; i < len(mf.g); i++ {
			mf.iter[i] = 0
		}
		for {
			f := mf.dfs(s, t, inf)
			if f == 0 {
				break
			}
			flow += f
		}
	}
}

func (mf *maxFlow) MinCut(s int) []bool {
	vis := make([]bool, len(mf.g))
	q := []int{s}
	for len(q) != 0 {
		p := q[0]
		q = q[1:]
		for _, e := range mf.g[p] {
			if e.cap != 0 && !vis[e.to] {
				vis[e.to] = true
				q = append(q, e.to)
			}
		}
	}
	return vis
}
```
