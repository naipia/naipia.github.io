---
title: "ユークリッドの互除法"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - math
---

```go
func gcd(m, n int) int {
	for n != 0 {
		m, n = n, m%n
	}
	return m
}

```

### 拡張ユークリッドの互除法

```go
func extgcd(m, n int) (int, int, int) {
	x, y := 1, 0
	u, v := 0, 1
	for n != 0 {
		k := m / n
		m, n = n, m-k*n
		x, u = u, x-k*u
		y, v = v, y-k*v
	}
	return m, x, y
}

```
