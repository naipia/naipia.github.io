---
title: "Binary Indexed Tree"
math: true
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - data structure
---

- `func newBinaryIndexedTree(n int) *binaryIndexedTree`
- `func (t binaryIndexedTree) Sum(i int) int`
- `func (t binaryIndexedTree) RangeSum(l, r int) int`
- `func (t binaryIndexedTree) Add(i, x int)`
- `func (t binaryIndexedTree) LowerBound(x int) int`

```go
type binaryIndexedTree []int

func newBinaryIndexedTree(n int) *binaryIndexedTree {
	bit := make(binaryIndexedTree, n+1)
	return &bit
}

func (t binaryIndexedTree) Sum(i int) int {
	s := 0
	for i++; i > 0; i -= i & -i {
		s += t[i]
	}
	return s
}

func (t binaryIndexedTree) RangeSum(l, r int) int {
	return t.Sum(r-1) - t.Sum(l-1)
}

func (t binaryIndexedTree) Add(i, x int) {
	for i++; i < len(t) && i > 0; i += i & -i {
		t[i] += x
	}
}

func (t binaryIndexedTree) LowerBound(x int) int {
	idx, k := 0, 1
	for k < len(t) {
		k <<= 1
	}
	for k >>= 1; k > 0; k >>= 1 {
		if idx+k < len(t) && t[idx+k] < x {
			x -= t[idx+k]
			idx += k
		}
	}
	return idx
}

```
