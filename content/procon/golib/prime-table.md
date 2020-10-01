---
title: "素数テーブル"
math: false
categories:
  - 競技プログラミング
tags:
  - Go言語
  - library
  - math
---

```go
func newPrimeTable(n int) []bool {
	pt := make([]bool, n+1)
	for i := 2; i <= n; i++ {
		pt[i] = true
	}
	for m := 2; m*m <= n; m++ {
		if !pt[m] {
			continue
		}
		for i := m * m; i <= n; i += m {
			pt[i] = false
		}
	}
	return pt
}

```
