---
title: "Union-Find"
math: true
categories:
  - 競技プログラミング
tags:
  - Go言語
  - library
  - data structure
---

- `func newUnionFind(n int) *unionFind`
- `func (uf unionFind) Find(x int) int`
- `func (uf unionFind) Unite(x, y int) bool`
- `func (uf unionFind) Unite(x, y int) bool`

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

func (uf unionFind) Same(x, y int) bool {
	return uf.Find(x) == uf.Find(y)
}

func (uf unionFind) Size(x int) int {
	return -uf[uf.Find(x)]
}

```
