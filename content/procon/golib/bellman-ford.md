---
title: "Bellman-Ford"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - graph
---

```go
type edge struct{ v, to, co int }

func bellmanFord(es []edge, n, s, inf int) ([]int, bool) {
	dist := make([]int, n)
	for i := 0; i < n; i++ {
		dist[i] = inf
	}
	dist[s] = 0
	for i := 0; i < n-1; i++ {
		for _, e := range es {
			if dist[e.v] != inf && dist[e.to] > dist[e.v]+e.co {
				dist[e.to] = dist[e.v] + e.co
			}
		}
	}
	cycle := false
	for _, e := range es {
		if dist[e.v] != inf && dist[e.to] > dist[e.v]+e.co {
			cycle = true
		}
	}
	return dist, cycle
}

```
