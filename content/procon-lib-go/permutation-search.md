---
title: "順列全探索"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - search
---

```go
func permutationSearch(n int, fn func(a []int)) {
	a := make([]int, n)
	for i := 0; i < n; i++ {
		a[i] = i
	}
	swap := func(i, j int) { a[i], a[j] = a[j], a[i] }

	fn(a)
	for i := n - 2; i >= 0; i-- {
		if a[i] > a[i+1] {
			continue
		}
		for j := n - 1; j >= 0; j-- {
			if a[i] > a[j] {
				continue
			}
			swap(i, j)
			for k := i + 1; k < (n+i+1)/2; k++ {
				swap(k, n-(k-i))
			}
			fn(a)
			i = n - 1
			break
		}
	}
}

```
