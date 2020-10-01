---
title: "Mod"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - math
---

```go
func powMod(a, n, M int) int {
	r := 1
	for n > 0 {
		if n&1 == 1 {
			r = r * a % M
		}
		a = a * a % M
		n >>= 1
	}
	return r
}

func invMod(a, M int) int {
	p, x, u := M, 1, 0
	for p != 0 {
		t := a / p
		a, p = p, a-t*p
		x, u = u, x-t*u
	}
	return x
}

```
