---
title: "素因数分解"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - math
---

```go
func factorize(n int) map[int]int {
	pf := map[int]int{}
	for i := 2; i*i <= n; i++ {
		for n%i == 0 {
			pf[i]++
			n /= i
		}
	}
	if n != 1 {
		pf[n] = 1
	}
	return pf
}

```

### 高速素因数分解

```go
type spft []int

func newSmallestPrimeFactorTable(n int) *spft {
	pt := make(spft, n+1)
	for i := 0; i <= n; i++ {
		pt[i] = i
	}
	for m := 2; m*m <= n; m++ {
		if pt[m] < m {
			continue
		}
		for i := m * m; i <= n; i += m {
			if pt[i] == i {
				pt[i] = m
			}
		}
	}
	return &pt
}

func (f spft) factorize(a int) map[int]int {
	pf := map[int]int{}
	for a != 1 {
		pf[f[a]]++
		a /= f[a]
	}
	return pf
}

```
