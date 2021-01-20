---
title: "Matrix"
math: false
categories:
  - 競技プログラミング
tags:
  - Go
  - library
  - math
---

```go
type Matrix struct {
	arr      []int
	row, col int
}

func newMatrix(a []int, row, col int) *Matrix {
	arr := make([]int, row*col)
	copy(arr, a)
	return &Matrix{arr, row, col}
}

func newIMatrix(n int) *Matrix {
	arr := make([]int, n*n)
	for i := 0; i < n; i++ {
		arr[i+i*n] = 1
	}
	return &Matrix{arr, n, n}
}

// At function
func (m Matrix) At(i, j int) int { return m.arr[i*m.col+j] }

// AddMatrix function
func AddMatrix(a, b *Matrix) *Matrix {
	if a.row != b.row || a.col != b.col {
		return &Matrix{}
	}
	res := newMatrix(nil, a.row, a.col)
	for i := 0; i < a.row*a.col; i++ {
		res.arr[i] = a.arr[i] + b.arr[i]
	}
	return res
}

// SubMatrix function
func SubMatrix(a, b *Matrix) *Matrix {
	if a.row != b.row || a.col != b.col {
		return &Matrix{}
	}
	res := newMatrix(nil, a.row, a.col)
	for i := 0; i < a.row*a.col; i++ {
		res.arr[i] = a.arr[i] - b.arr[i]
	}
	return res
}

// MulMatrix function
func MulMatrix(a, b *Matrix) *Matrix {
	if a.col != b.row {
		return &Matrix{}
	}
	res := newMatrix(nil, a.row, b.col)
	for i := 0; i < res.row; i++ {
		for j := 0; j < res.col; j++ {
			for k := 0; k < a.col; k++ {
				res.arr[i*res.col+j] += a.arr[i*a.col+k] * b.arr[k*b.col+j]
			}
		}
	}
	return res
}

// PowMatrix function
func PowMatrix(a *Matrix, n int) *Matrix {
	if a.row != a.col {
		return &Matrix{}
	}
	t := newMatrix(a.arr, a.row, a.col)
	res := newIMatrix(a.row)
	for n > 0 {
		if n&1 == 1 {
			res = MulMatrix(res, t)
		}
		t = MulMatrix(t, t)
		n >>= 1
	}
	return res
}
```
