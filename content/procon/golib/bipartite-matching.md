---
title: "二部グラフ最大マッチング"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - graph
---

```go
func bipartiteMatching(g [][]int) (int, []int) {
	used := make([]int, len(g))
	match := make([]int, len(g))
	for i := 0; i < len(g); i++ {
		match[i] = -1
	}
	cnt := 0

	var fn func(v int) bool
	fn = func(v int) bool {
		if used[v] == cnt {
			return false
		}
		used[v] = cnt
		for _, to := range g[v] {
			if match[to] == -1 || fn(match[to]) {
				match[v], match[to] = to, v
				return true
			}
		}
		return false
	}

	res := 0

	for i := 0; i < len(g); i++ {
		cnt++
		if match[i] == -1 && fn(i) {
			res++
		}
	}

	return res, match
}

```
