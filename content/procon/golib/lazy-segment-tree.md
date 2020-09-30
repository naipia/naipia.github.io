---
title: "Lazy Segment Tree"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - data structure
---

- `func newLazySegmentTree(n int, e func() S, id func() F, f func(a, b S) S, g func(a S, x F) S, h func(a, x F) F,) *lazySegmentTree`
- `func (s *lazySegmentTree) Set(i int, a S)`
- `func (s *lazySegmentTree) Build()`
- `func (s *lazySegmentTree) Get(k int) S`
- `func (s *lazySegmentTree) Update(l, r int, x F)`
- `func (s *lazySegmentTree) Query(l, r int) S`
- `func (s *lazySegmentTree) MaxRight(l int, f func(v S) bool) int`
- `func (s *lazySegmentTree) MinLeft(r int, f func(v S) bool) int`

```go
type S struct{}
type F struct{}

type lazySegmentTree struct {
	d  []S
	lz []F
	n  int
	sz int
	lg int
	e  func() S
	id func() F
	f  func(a, b S) S
	g  func(a S, x F) S
	h  func(a, x F) F
}

func newLazySegmentTree(n int,
	e func() S,
	id func() F,
	f func(a, b S) S,
	g func(a S, x F) S,
	h func(a, x F) F,
) *lazySegmentTree {
	sz, lg := 1, 0
	for sz < n {
		sz <<= 1
		lg++
	}
	d := make([]S, 2*sz)
	lz := make([]F, 2*sz)
	for i := 0; i < 2*sz; i++ {
		d[i] = e()
		lz[i] = id()
	}
	s := lazySegmentTree{d, lz, n, sz, lg, e, id, f, g, h}
	return &s
}

func (s *lazySegmentTree) Set(i int, a S) {
	s.d[i+s.sz] = a
}

func (s *lazySegmentTree) Build() {
	for i := s.sz - 1; i > 0; i-- {
		s.d[i] = s.f(s.d[2*i], s.d[2*i+1])
	}
}

func (s *lazySegmentTree) Get(k int) S {
	k += s.sz
	for i := s.lg; i > 0; i-- {
		s.prop(k >> i)
	}
	return s.d[k]
}

func (s *lazySegmentTree) Update(l, r int, x F) {
	l += s.sz
	r += s.sz
	for i := s.lg; i > 0; i-- {
		if l>>i<<i != l {
			s.prop(l >> i)
		}
		if r>>i<<i != r {
			s.prop((r - 1) >> i)
		}
	}

	for ll, rr := l, r; ll < rr; ll, rr = ll>>1, rr>>1 {
		if ll&1 == 1 {
			s.apply(ll, x)
			ll++
		}
		if rr&1 == 1 {
			rr--
			s.apply(rr, x)
		}
	}

	for i := 1; i <= s.lg; i++ {
		if l>>i<<i != l {
			k := l >> i
			s.d[k] = s.f(s.d[2*k], s.d[2*k+1])
		}
		if r>>i<<i != r {
			k := (r - 1) >> i
			s.d[k] = s.f(s.d[2*k], s.d[2*k+1])
		}
	}
}

func (s *lazySegmentTree) Query(l, r int) S {
	vl, vr := s.e(), s.e()
	l += s.sz
	r += s.sz

	for i := s.lg; i > 0; i-- {
		if l>>i<<i != l {
			s.prop(l >> i)
		}
		if r>>i<<i != r {
			s.prop(r >> i)
		}
	}

	for ; l < r; l, r = l>>1, r>>1 {
		if l&1 == 1 {
			vl = s.f(vl, s.d[l])
			l++
		}
		if r&1 == 1 {
			r--
			vr = s.f(s.d[r], vr)
		}
	}
	return s.f(vl, vr)
}

func (s *lazySegmentTree) MaxRight(l int, f func(v S) bool) int {
	if l == s.n {
		return s.n
	}
	v := s.e()
	l += s.sz
	for i := s.lg; i > 0; i-- {
		s.prop(l >> i)
	}

	for {
		for l&1 == 0 {
			l >>= 1
		}
		if !f(s.f(v, s.d[l])) {
			for l < s.sz {
				l <<= 1
				if f(s.f(v, s.d[l])) {
					v = s.f(v, s.d[l])
					l++
				}
			}
			return l - s.sz
		}
		v = s.f(v, s.d[l])
		l++
		if l&-l == l {
			break
		}
	}

	return s.n
}

func (s *lazySegmentTree) MinLeft(r int, f func(v S) bool) int {
	if r == 0 {
		return 0
	}
	v := s.e()
	r += s.sz
	for i := s.lg; i > 0; i-- {
		s.prop((r - 1) >> i)
	}

	for {
		r--
		for r > 1 && r&1 == 1 {
			r >>= 1
		}
		if !f(s.f(v, s.d[r])) {
			for r < s.sz {
				r = r<<1 + 1
				if f(s.f(v, s.d[r])) {
					v = s.f(v, s.d[r])
					r--
				}
			}
			return r + 1 - s.sz
		}
		v = s.f(v, s.d[r])
		if r&-r == r {
			break
		}
	}

	return 0
}

func (s *lazySegmentTree) prop(k int) {
	s.apply(2*k, s.lz[k])
	s.apply(2*k+1, s.lz[k])
	s.lz[k] = s.id()
}

func (s *lazySegmentTree) apply(k int, x F) {
	s.d[k] = s.g(s.d[k], x)
	if k < s.sz {
		s.lz[k] = s.h(s.lz[k], x)
	}
}
 
```
