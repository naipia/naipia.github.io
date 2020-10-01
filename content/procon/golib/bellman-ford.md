---
title: "Bellman-Ford"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - graph
---

```go
type edge struct{ v, to, co int }

func bellmanFord(es []edge, n, s, inf int) []int {
	dist := make([]int, n)
	for i := 0; i < n-1; i++ {
		dist[i] = inf
	}
	dist[s] = 0
	chmin := func(a *int, b int) {
		if *a > b {
			*a = b
		}
	}
	for i := 0; i < n-1; i++ {
		for _, e := range es {
			if dist[e.v] == inf {
				continue
			}
			chmin(&dist[e.to], dist[e.v]+e.co)
		}
	}
	for _, e := range es {
		if dist[e.v]+e.co < dist[e.to] {
			return []int{}
		}
	}
	return dist
}

```
