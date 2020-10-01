---
title: "組合せ"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - math
---

```go
type combination struct {
	fac, ifac, inv []int
	m              int
}

func newCombination(n, M int) *combination {
	fac := make([]int, n+1)
	ifac := make([]int, n+1)
	inv := make([]int, n+1)

	fac[0], fac[1] = 1, 1
	ifac[0], ifac[1] = 1, 1
	inv[1] = 1

	for i := 1; i < n; i++ {
		fac[i+1] = fac[i] * (i + 1) % M
		inv[i+1] = M - inv[M%(i+1)]*(M/(i+1))%M
		ifac[i+1] = ifac[i] * inv[i+1] % M
	}

	return &combination{fac, ifac, inv, M}
}

func (c *combination) P(m, n int) int {
	return c.fac[m] * c.ifac[m-n] % c.m
}
func (c *combination) C(m, n int) int {
	return c.fac[m] * c.ifac[n] % c.m * c.ifac[m-n] % c.m
}
func (c *combination) H(n, r int) int {
	return c.C(n+r-1, r)
}

```
