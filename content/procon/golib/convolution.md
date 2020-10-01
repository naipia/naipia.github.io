---
title: "Convolution"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - math
---

```go
func convolutionMod(a, b []int, M int) []int {
	n1, n2 := len(a), len(b)
	n := n1 + n2 - 1
	if n1 == 0 || n2 == 0 {
		return []int{}
	}

	z := 1 << ceilPow2(n)
	aa, bb := make([]int, z), make([]int, z)
	copy(aa, a)
	copy(bb, b)
	a, b = aa, bb

	butterfly(a, M)
	butterfly(b, M)
	for i := 0; i < z; i++ {
		a[i] = a[i] * b[i] % M
	}
	butterflyInv(a, M)
	a = a[:n]
	iz := invMod(z, M)
	for i := 0; i < n; i++ {
		a[i] = a[i] * iz % M
		if a[i] < 0 {
			a[i] += M
		}
	}

	return a
}

func convolution(a, b []int) []int {
	n1, n2 := len(a), len(b)
	n := n1 + n2 - 1
	if n1 == 0 || n2 == 0 {
		return []int{}
	}

	MOD1 := 754974721
	MOD2 := 167772161
	MOD3 := 469762049
	M2M3 := MOD2 * MOD3
	M1M3 := MOD1 * MOD3
	M1M2 := MOD1 * MOD2
	M1M2M3 := MOD1 * MOD2 * MOD3

	i1 := invMod(M2M3, MOD1)
	i2 := invMod(M1M3, MOD2)
	i3 := invMod(M1M2, MOD3)

	c1 := convolutionMod(a, b, MOD1)
	c2 := convolutionMod(a, b, MOD2)
	c3 := convolutionMod(a, b, MOD3)

	c := make([]int, n)
	offset := []int{0, 0, M1M2M3, 2 * M1M2M3, 3 * M1M2M3}

	for i := 0; i < n; i++ {
		x := 0
		x += c1[i] * i1 % MOD1 * M2M3
		x += c2[i] * i2 % MOD2 * M1M3
		x += c3[i] * i3 % MOD3 * M1M2
		diff := c1[i] - x%MOD1
		if diff < 0 {
			diff += MOD1
		}
		x -= offset[diff%5]
		c[i] = x
	}

	return c
}

func primitiveRoot(m int) int {
	if m == 2 {
		return 1
	}
	if m == 167772161 || m == 469762049 || m == 998244353 {
		return 3
	}
	if m == 754974721 {
		return 11
	}
	divs := make([]int, 20)
	divs[0] = 2
	cnt := 1
	x := (m - 1) / 2
	for x%2 == 0 {
		x /= 2
	}
	for i := 3; i*i <= x; i += 2 {
		if x%i == 0 {
			divs[cnt] = i
			cnt++
			for x%i == 0 {
				x /= i
			}
		}
	}
	if x > 1 {
		divs[cnt] = x
		cnt++
	}
	for g := 2; ; g++ {
		ok := true
		for i := 0; i < cnt; i++ {
			if powMod(g, (m-1)/divs[i], m) == 1 {
				ok = false
				break
			}
		}
		if ok {
			return g
		}
	}
}

func powMod(a, n, M int) int {
	if M == 1 {
		return 0
	}
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

func ceilPow2(n int) int {
	x := 0
	for 1<<x < n {
		x++
	}
	return x
}

func bsf(n int) int {
	x := 0
	for n&(1<<x) == 0 {
		x++
	}
	return x
}

func butterfly(a []int, M int) {
	g := primitiveRoot(M)
	n := len(a)
	h := ceilPow2(n)

	se := make([]int, 30)
	es, ies := make([]int, 30), make([]int, 30)
	cnt2 := bsf(M - 1)
	e := powMod(g, (M-1)>>cnt2, M)
	ie := invMod(e, M)
	for i := cnt2; i >= 2; i-- {
		es[i-2] = e
		ies[i-2] = ie
		e = e * e % M
		ie = ie * ie % M
	}
	now := 1
	for i := 0; i <= cnt2-2; i++ {
		se[i] = es[i] * now % M
		now = now * ies[i] % M
	}
	for ph := 1; ph <= h; ph++ {
		w := 1 << (ph - 1)
		p := 1 << (h - ph)
		now := 1
		for s := 0; s < w; s++ {
			offset := s << (h - ph + 1)
			for i := 0; i < p; i++ {
				l := a[i+offset]
				r := a[i+offset+p] * now % M
				a[i+offset] = (l + r) % M
				a[i+offset+p] = (M + l - r) % M
			}
			now = now * se[bsf(^s)] % M
		}
	}
}

func butterflyInv(a []int, M int) {
	g := primitiveRoot(M)
	n := len(a)
	h := ceilPow2(n)

	sie := make([]int, 30)
	es, ies := make([]int, 30), make([]int, 30)
	cnt2 := bsf(M - 1)
	e := powMod(g, (M-1)>>cnt2, M)
	ie := invMod(e, M)
	for i := cnt2; i >= 2; i-- {
		es[i-2] = e
		ies[i-2] = ie
		e = e * e % M
		ie = ie * ie % M
	}
	now := 1
	for i := 0; i <= cnt2-2; i++ {
		sie[i] = ies[i] * now % M
		now = now * es[i] % M
	}
	for ph := h; ph >= 1; ph-- {
		w := 1 << (ph - 1)
		p := 1 << (h - ph)
		inow := 1
		for s := 0; s < w; s++ {
			offset := s << (h - ph + 1)
			for i := 0; i < p; i++ {
				l := a[i+offset]
				r := a[i+offset+p]
				a[i+offset] = (l + r) % M
				a[i+offset+p] = (M + l - r) * inow % M
			}
			inow = inow * sie[bsf(^s)] % M
		}
	}
}

```
