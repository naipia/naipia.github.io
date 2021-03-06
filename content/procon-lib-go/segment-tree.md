---
title: "Segment Tree"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - data structure
---

```go
type S struct{ v int }

func op(a, b S) S {
	if a.v > b.v {
		return a
	}
	return b
}

func e() S {
	return S{0}
}

type segTree struct {
	d  []S
	n  int
	sz int
	e  func() S
	f  func(a, b S) S
}

func newSegTree(n int, e func() S, op func(a, b S) S) *segTree {
	sz := 1
	for sz < n {
		sz <<= 1
	}
	s := segTree{make([]S, 2*sz), n, sz, e, op}
	for i := 0; i < 2*sz; i++ {
		s.d[i] = e()
	}
	return &s
}

func (s *segTree) Build(arr []S) {
	for i, v := range arr {
		s.d[i+s.sz] = v
	}
	for i := s.sz - 1; i > 0; i-- {
		s.d[i] = s.f(s.d[2*i], s.d[2*i+1])
	}
}

func (s *segTree) Get(i int) S {
	return s.d[i+s.sz]
}

func (s *segTree) Set(i int, a S) {
	i += s.sz
	s.d[i] = a
	for i >>= 1; i > 0; i >>= 1 {
		s.d[i] = s.f(s.d[2*i], s.d[2*i+1])
	}
}

func (s *segTree) Query(l, r int) S {
	vl, vr := s.e(), s.e()
	l += s.sz
	r += s.sz
	for l < r {
		if l&1 == 1 {
			vl = s.f(vl, s.d[l])
			l++
		}
		if r&1 == 1 {
			r--
			vr = s.f(s.d[r], vr)
		}
		l >>= 1
		r >>= 1
	}
	return s.f(vl, vr)
}

func (s *segTree) MaxRight(l int, f func(v S) bool) int {
	if l == s.n {
		return s.n
	}
	v := s.e()
	l += s.sz

	for {
		for ; l&1 == 0; l >>= 1 {
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

func (s *segTree) MinLeft(r int, f func(v S) bool) int {
	if r == 0 {
		return 0
	}
	v := s.e()
	r += s.sz

	for {
		for r--; r > 1 && r&1 == 1; r >>= 1 {
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

```
