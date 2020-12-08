---
title: "組合せ"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - math
---

```go
type combination struct {
	fact, ifact, inv []int
	m              int
}

func newCombination(n, M int) *combination {
	fact := make([]int, n+1)
	ifact := make([]int, n+1)
	inv := make([]int, n+1)

	fact[0], fact[1] = 1, 1
	ifact[0], ifact[1] = 1, 1
	inv[1] = 1

	for i := 1; i < n; i++ {
		fact[i+1] = fact[i] * (i + 1) % M
		inv[i+1] = M - inv[M%(i+1)]*(M/(i+1))%M
		ifact[i+1] = ifact[i] * inv[i+1] % M
	}

	return &combination{fact, ifact, inv, M}
}

func (c *combination) P(m, n int) int {
	return c.fact[m] * c.ifact[m-n] % c.m
}
func (c *combination) C(m, n int) int {
	return c.fact[m] * c.ifact[n] % c.m * c.ifact[m-n] % c.m
}
func (c *combination) H(n, r int) int {
	return c.C(n+r-1, r)
}

```
