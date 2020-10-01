---
title: "Z Algorithm"
math: false
categories:
  - 競技プログラミング
tags:
  - library
  - string
---

```go
func zalgorithm(s []string) []int {
	n := len(s)
	z := make([]int, n)
	z[0] = n
	for i, j := 1, 0; i < n; {
		for i+j < n && s[j] == s[i+j] {
			j++
		}
		z[i] = j
		if j == 0 {
			i++
			continue
		}
		k := 1
		for ; i+k < n && k+z[k] < j; k++ {
			z[i+k] = z[k]
		}
		i += k
		j -= k
	}
	return z
}

```
