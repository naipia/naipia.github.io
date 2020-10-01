---
title: "ビット全探索"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - search
---

```go
func bitExhaustiveSearch(n int, fn func(a []bool)) {
	all := 1 << uint(n)
	a := make([]bool, n)
	for i := 0; i < all; i++ {
		for j := 0; j < n; j++ {
			if i>>uint(j)&1 == 1 {
				a[j] = true
			} else {
				a[j] = false
			}
		}
		fn(a)
	}
}

```
