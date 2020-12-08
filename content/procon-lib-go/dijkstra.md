---
title: "Dijkstra"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - graph
---

```go
type edge struct{ to, co int }
type vert struct{ d, v int }
type pqForDijkstra []vert

func (pq pqForDijkstra) Len() int            { return len(pq) }
func (pq pqForDijkstra) Swap(i, j int)       { pq[i], pq[j] = pq[j], pq[i] }
func (pq pqForDijkstra) Less(i, j int) bool  { return pq[i].d < pq[j].d }
func (pq *pqForDijkstra) Push(x interface{}) { *pq = append(*pq, x.(vert)) }
func (pq *pqForDijkstra) Pop() interface{} {
	x := (*pq)[len(*pq)-1]
	*pq = (*pq)[0 : len(*pq)-1]
	return x
}

func dijkstra(es [][]edge, s, inf int) []int {
	d := make([]int, len(es))
	for i := 0; i < len(es); i++ {
		d[i] = inf
	}
	d[s] = 0
	pq := &pqForDijkstra{vert{0, s}}
	heap.Init(pq)
	for len(*pq) != 0 {
		p := heap.Pop(pq).(vert)
		if p.d > d[p.v] {
			continue
		}
		for _, e := range es[p.v] {
			cost := d[p.v] + e.co
			if cost < d[e.to] {
				d[e.to] = cost
				heap.Push(pq, vert{d[e.to], e.to})
			}
		}
	}
	return d
}

```
