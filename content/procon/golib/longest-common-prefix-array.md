---
title: "LCP Array"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - string
---

```go
func newLongestCommonPrefixArray(s string, sa []int) []int {
	n := len(s)
	lcp := make([]int, n-1)
	ra := make([]int, n)
	for i := 0; i < n; i++ {
		ra[sa[i]] = i
	}

	for i, h := 0, 0; i < n; i++ {
		if ra[i] == 0 {
			continue
		}
		if h > 0 {
			h--
		}
		for j := sa[ra[i]-1]; j+h < n && i+h < n; h++ {
			if s[j+h] != s[i+h] {
				break
			}
		}
		lcp[ra[i]-1] = h
	}

	return lcp
}

```
