---
title: "Floor Sum"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - math
---

```go
func floorSum(n, m, a, b int) int {
	ans := 0
	if a >= m {
		ans += (n - 1) * n * (a / m) / 2
		a %= m
	}
	if b >= m {
		ans += n * (b / m)
		b %= m
	}

	yma := (a*n + b) / m
	if yma == 0 {
		return ans
	}
	xma := yma*m - b
	ans += (n - (xma+a-1)/a) * yma
	ans += floorSum(yma, a, m, (a-xma%a)%a)
	return ans
}

```
