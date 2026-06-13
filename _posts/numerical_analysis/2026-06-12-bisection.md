---
title: "비선형 방정식의 해법 1: 이분법(Bisection Method)"
date: 2026-06-12 10:30:00 +0900
categories: ["수치해석", "비선형 방정식의 해법"]
tags: ["수치해석", "비선형방정식", "이분법"]
order: 1
math: true
---

# 개념
이분법은 구간 $[a,b]$에서 연속인 함수 $f(x)$의 해를 찾는 방법으로,
함수 $f(x)$가 $[a,b]$에서 서로 부호가 달라지면 그 구간 안에 적어도 하나의 해가 존재한다. 
이 원리를 이용해 해를 반복적으로 좁혀가는 방법이 이분법이다. 이는 해가 존재 하는 구간을 절반씩 줄여 근사적인 해를 찾는다.

# 알고리즘
1. 함수 $f(x)$가 $[a,b]$에서 연속이고, $f(a)$와 $f(b)$의 부호가 다르다고 가정한다. 그러면 $f(a)f(b)<0$이면 해가 그 사이에 존재한다.
2. 구간의 중간점 $c=\frac{a+b}{2}$을 계산한다.
3. $f(c)$의 부호에 따라 새로운 구간을 설정한다.
   $f(a)f(c)<0$ 이면 해는 구간 $[a,c]$에 있다.
   그렇지 않으면 해는 $[c,b]$에 있다.
4. 원하는 오차범위에 도달할 때까지 이 과정을 반복한다.

# 파이썬 코드

```python
import numpy as np
import matplotlib.pyplot as plt

def f(x):
    return x**2 - 2

def bisection_method(a, b, tol):
    if f(a) * f(b) >= 0:
        print("이분법을 적용할 수 없습니다.")
        return None

    steps = []

    while (b - a) / 2.0 > tol:
        midpoint = (a + b) / 2.0
        steps.append(midpoint)

        if f(midpoint) == 0:
            return midpoint, steps
        elif f(a) * f(midpoint) < 0:
            b = midpoint
        else:
            a = midpoint

    return (a + b) / 2.0, steps

root, steps = bisection_method(0, 2, 0.0001)

print(f"이분법으로 구한 해: {root}")
```

# 실행 결과

```text
이분법으로 구한 해: 1.4141845703125
```
