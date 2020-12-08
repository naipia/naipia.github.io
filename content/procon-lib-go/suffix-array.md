---
title: "Suffix Array"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - string
---

```go
func newSuffixArray(s string) []int {
	s2 := make([]int, len(s))
	for i, v := range s {
		s2[i] = int(v)
	}

	return sais(s2, 255)
}

func newSuffixArrayInts(s []int) []int {
	n := len(s)
	idx := make([]int, n)
	for i := 0; i < n; i++ {
		idx[i] = i
	}
	sort.Slice(idx, func(i, j int) bool {
		return s[idx[i]] < s[idx[j]]
	})

	s2 := make([]int, n)
	u := 0
	for i := 0; i < n; i++ {
		if i != 0 && s[idx[i-1]] != s[idx[i]] {
			u++
		}
		s2[idx[i]] = u
	}

	return sais(s2, u)
}

func sais(s []int, u int) []int {
	n := len(s)
	if n == 0 {
		return []int{}
	}
	if n == 1 {
		return []int{0}
	}
	if n == 2 {
		if s[0] < s[1] {
			return []int{0, 1}
		}
		return []int{1, 0}
	}

	sa := make([]int, n)
	ls := make([]bool, n)
	for i := n - 2; i >= 0; i-- {
		if s[i] == s[i+1] {
			ls[i] = ls[i+1]
		} else {
			ls[i] = s[i] < s[i+1]
		}
	}

	sumL, sumS := make([]int, u+1), make([]int, u+1)
	for i := 0; i < n; i++ {
		if !ls[i] {
			sumS[s[i]]++
		} else {
			sumL[s[i]+1]++
		}
	}

	for i := 0; i <= u; i++ {
		sumS[i] += sumL[i]
		if i < u {
			sumL[i+1] += sumS[i]
		}
	}

	induce := func(lms []int) {
		for i := 0; i < n; i++ {
			sa[i] = -1
		}
		buf := make([]int, u+1)
		copy(buf, sumS)
		for i := 0; i < len(lms); i++ {
			d := lms[i]
			if d == n {
				continue
			}
			sa[buf[s[d]]] = d
			buf[s[d]]++
		}

		copy(buf, sumL)
		sa[buf[s[n-1]]] = n - 1
		buf[s[n-1]]++
		for i := 0; i < n; i++ {
			v := sa[i]
			if v >= 1 && !ls[v-1] {
				sa[buf[s[v-1]]] = v - 1
				buf[s[v-1]]++
			}
		}
		copy(buf, sumL)
		for i := n - 1; i >= 0; i-- {
			v := sa[i]
			if v >= 1 && ls[v-1] {
				buf[s[v-1]+1]--
				sa[buf[s[v-1]+1]] = v - 1
			}
		}
	}

	lmsMap := make([]int, n+1)
	for i := 0; i <= n; i++ {
		lmsMap[i] = -1
	}
	m := 0
	for i := 1; i < n; i++ {
		if !ls[i-1] && ls[i] {
			lmsMap[i] = m
			m++
		}
	}
	lms := make([]int, 0, m)
	for i := 1; i < n; i++ {
		if !ls[i-1] && ls[i] {
			lms = append(lms, i)
		}
	}

	induce(lms)

	if m != 0 {
		sortedLms := make([]int, 0, m)
		for i := 0; i < n; i++ {
			if lmsMap[sa[i]] != -1 {
				sortedLms = append(sortedLms, sa[i])
			}
		}
		recS := make([]int, m)
		recU := 0
		recS[lmsMap[sortedLms[0]]] = 0
		for i := 1; i < m; i++ {
			l, r := sortedLms[i-1], sortedLms[i]
			endL, endR := n, n
			if lmsMap[l]+1 < m {
				endL = lms[lmsMap[l]+1]
			}
			if lmsMap[r]+1 < m {
				endR = lms[lmsMap[r]+1]
			}
			same := true
			if endL-l != endR-r {
				same = false
			} else {
				for l < endL {
					if s[l] != s[r] {
						break
					}
					r++
					l++
				}
				if l == n || s[l] != s[r] {
					same = false
				}
			}
			if !same {
				recU++
			}
			recS[lmsMap[sortedLms[i]]] = recU
		}

		recSa := sais(recS, recU)

		for i := 0; i < m; i++ {
			sortedLms[i] = lms[recSa[i]]
		}
		induce(sortedLms)
	}

	return sa
}

```
