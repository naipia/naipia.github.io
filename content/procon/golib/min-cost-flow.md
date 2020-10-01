---
title: "最小費用流"
math: false
categories:
  - 競技プログラミング
tags:
  - Go言語
  - library
  - graph
---

```go
const inf = 1 << 60

type edge struct{ from, to, cap, flow, cost int }
type _edge struct{ to, cap, rev, cost int }

type minCostFlow struct {
	n  int
	g  [][]_edge
	es [][2]int
}

func newMinCostFlow(n int) *minCostFlow {
	return &minCostFlow{n, make([][]_edge, n), [][2]int{}}
}

func (mcf *minCostFlow) AddEdge(from, to, cap, cost int) {
	mcf.es = append(mcf.es, [2]int{from, len(mcf.g[from])})
	mcf.g[from] = append(mcf.g[from], _edge{to, cap, len(mcf.g[to]), cost})
	mcf.g[to] = append(mcf.g[to], _edge{from, 0, len(mcf.g[from]) - 1, -cost})
}

func (mcf *minCostFlow) Edge(i int) edge {
	v := mcf.es[i]
	e := mcf.g[v[0]][v[1]]
	r := mcf.g[e.to][e.rev]
	return edge{v[0], e.to, e.cap + r.cap, r.cap, e.cost}
}

func (mcf *minCostFlow) Edges() []edge {
	es := []edge{}
	for i := 0; i < len(mcf.es); i++ {
		es = append(es, mcf.Edge(i))
	}
	return es
}

func (mcf *minCostFlow) Flow(s, t, f int) (int, int) {
	n := mcf.n
	flow, cost := 0, 0
	pv, pe := make([]int, n), make([]int, n)
	h, d := make([]int, n), make([]int, n)

	min := func(a, b int) int {
		if a < b {
			return a
		}
		return b
	}

	for f > 0 {
		for i := 0; i < n; i++ {
			d[i] = inf
		}
		d[s] = 0
		pq := &pqForMinCostFlow{}
		heap.Push(pq, [2]int{0, s})
		for len(*pq) != 0 {
			p := heap.Pop(pq).([2]int)
			v := p[1]
			if d[v] < p[0] {
				continue
			}
			if v == t {
				break
			}
			for i := 0; i < len(mcf.g[v]); i++ {
				e := mcf.g[v][i]
				if e.cap == 0 {
					continue
				}
				if co := d[v] + e.cost + h[v] - h[e.to]; d[e.to] > co {
					d[e.to] = co
					pv[e.to], pe[e.to] = v, i
					heap.Push(pq, [2]int{d[e.to], e.to})
				}
			}
		}
		if d[t] == inf {
			return -1, 0
		}
		for i := 0; i < n; i++ {
			h[i] += d[i]
		}
		d := f
		for v := t; v != s; v = pv[v] {
			d = min(d, mcf.g[pv[v]][pe[v]].cap)
		}
		f -= d
		flow += d
		cost += d * h[t]
		for v := t; v != s; v = pv[v] {
			e := &mcf.g[pv[v]][pe[v]]
			e.cap -= d
			mcf.g[v][e.rev].cap += d
		}
	}

	return flow, cost
}

type pqForMinCostFlow [][2]int

func (pq pqForMinCostFlow) Len() int            { return len(pq) }
func (pq pqForMinCostFlow) Swap(i, j int)       { pq[i], pq[j] = pq[j], pq[i] }
func (pq pqForMinCostFlow) Less(i, j int) bool  { return pq[i][0] < pq[j][0] }
func (pq *pqForMinCostFlow) Push(x interface{}) { *pq = append(*pq, x.([2]int)) }
func (pq *pqForMinCostFlow) Pop() interface{} {
	x := (*pq)[len(*pq)-1]
	*pq = (*pq)[0 : len(*pq)-1]
	return x
}

```
