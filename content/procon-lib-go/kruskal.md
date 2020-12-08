---
title: "最小全域木"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - graph
---

```go
type unionFind []int

func newUnionFind(n int) *unionFind {
	uf := make(unionFind, n)
	for i := 0; i < n; i++ {
		uf[i] = -1
	}
	return &uf
}

func (uf unionFind) Find(x int) int {
	if uf[x] < 0 {
		return x
	}
	uf[x] = uf.Find(uf[x])
	return uf[x]
}

func (uf unionFind) Unite(x, y int) bool {
	x, y = uf.Find(x), uf.Find(y)
	if x != y {
		if uf[x] > uf[y] {
			x, y = y, x
		}
		uf[x] += uf[y]
		uf[y] = x
		return true
	}
	return false
}

func (uf unionFind) Size(x int) int {
	return -uf[uf.Find(x)]
}

type edge struct{ v, to, co int }

func kruskal(es []edge, n int) int {
	uf := newUnionFind(n)
	sort.Slice(es, func(i, j int) bool { return es[i].co < es[j].co })
	co := 0
	for i := 0; i < len(es); i++ {
		if uf.Unite(es[i].v, es[i].to) {
			co += es[i].co
		}
	}
	return co
}

```
