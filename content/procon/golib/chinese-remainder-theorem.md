---
title: "中国余剰定理"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - math
---

```go
func crt(r []int, m []int) (int, int) {
	r0, m0 := 0, 1

	invGcd := func(a, p int) (int, int) {
		x, u := 1, 0
		for p != 0 {
			t := a / p
			a, p = p, a-t*p
			x, u = u, x-t*u
		}
		return a, x
	}

	for i := 0; i < len(r); i++ {
		r1, m1 := r[i], m[i]
		if m0 < m1 {
			r0, r1 = r1, r0
			m0, m1 = m1, m0
		}

		if m0%m1 == 0 {
			if r0%m1 != r1 {
				return 0, 0
			}
			continue
		}

		g, im := invGcd(m0, m1)
		if (r1-r0)%g != 0 {
			return 0, 0
		}

		u := m1 / g
		x := (r1 - r0) / g % u * im % u
		r0 += m0 * x
		m0 *= u
	}

	return (r0%m0 + m0) % m0, m0
}

```
