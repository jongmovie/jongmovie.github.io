---
title: "비선형 방정식의 해법 1: 이분법(Bisection Method)"
date: 2026-06-12 10:30:00 +0900
categories: ["수치해석", "비선형 방정식의 해법"]
tags: ["수치해석", "비선형방정식", "이분법"]
order: 1
math: true
---

# 개념
이분법은 구간 $[a,b]$에서 연속인 함수 $f(x)$의 해를 찾는 방법으로, 중간값의 정리를 이용한다.

> **중간값 정리**
>
> 함수 $f:[a,b]\to \mathbb{R}$가 연속이고 $f(a)f(b)<0$이면,  
> $f(c)=0$인 점 $c\in(a,b)$가 적어도 하나 존재한다.

이 원리를 이용해 해를 반복적으로 좁혀가는 방법이 이분법이다. 이는 해가 존재 하는 구간을 절반씩 줄여 근사적인 해를 찾는다.

# 알고리즘
1. $f(a_0)f(b_0)<0$ 인 $a_0, b_0$를 선택한다.
2. 구간의 중간점 $m_0=\frac{a_0+b_0}{2}$을 계산한다.
3. $f(a_0)f(m_0)<0$ 이면 $a_1=a_0, b_1=m_0$
   $f(m_0)f(b_0)<0$ 이면 $a_1=m_0, b_1=b_0$
4. $|b_n-a_n|<\epsilon$ 또는 $|f(m_n)|<\epsilon$이 될 때까지 반복한다.

#  수렴성
함수 $f(x)$가 폐구간 $[a,b]$에서 연속이고 $f(a)f(b)<0$이면, 이분법에 의하여 만들어진 수열 $\{m_n\}$은 해 $p$에 수렴한다.

$$
|m_n-p|\le\frac{b-a}{2^n}
$$

# 이분법의 반복 횟수
이분법으로 허용하는 에러범위에서 근을 찾으려면 적어도 다음 부등식을 만족시키는 만큼 반복이 필요하다.


$$
n\ge\frac{\ln{b_0-a_0}-\ln{\epsilon}{\ln2}
$$

증명
근을 포함하는 구간의 길이는 항상 이전 구간 길이의 절반이므로 다음식이 성립한다.

$$
b_n-a_n=\frac{1}{2}(b_{n-1}-a_{n-1})=\frac{1}{2^{n-1}}(b_0-a_0)
$$

따라서 실제 근을 $p$라고 하고 $m_n=\frac{a_n+b_n}{2}$라고 하면

$$
|p-m_n|\le m_n-a_n=\frac{1}{2}(b_n-a_n)=\frac{1}{2^n}(b_0-a_0)\le \epsilon
$$

그러므로 양변에 로그를 취하면 다음과 같다.

$$
n\ge\frac{\ln{b_0-a_0}-\ln{\epsilon}{\ln2}
$$

# 파이썬 코드

```python
import numpy as np
import matplotlib.pyplot as plt

def f(x):
    return x**2 - 2

# Bisection Method
def Bisection_Method(a, b, tol):
    if f(a) * f(b) >= 0:
        print("Bisection method cannot be applied.")
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

root, steps = Bisection_Method(0, 2, 0.0001)

print(f"Approximate root by bisection method: {root}")
print(f"Number of iterations: {len(steps)}")

# Plot
x = np.linspace(0, 2, 400)
y = f(x)

plt.figure(figsize=(8, 5))
plt.plot(x, y, label="Function $f(x)=x^2-2$", color="blue")
plt.axhline(0, color="black", linewidth=0.8)
plt.axvline(root, color="red", linestyle="--", label=f"Approximate root = {root:.5f}")

# Visualize midpoints calculated by the bisection method
for m in steps:
    plt.plot(m, f(m), "ro", markersize=4)

# Graph settings
plt.title("Finding a Root Using the Bisection Method")
plt.xlabel("x")
plt.ylabel("f(x)")
plt.legend()
plt.grid(True)
plt.tight_layout()

# Save and show
plt.savefig("bisection_method_graph.png")
plt.show()
```

# 실행 결과
![Bisection Method Graph](/assets/images/Bisection_method.png)
```text
Approximate root by bisection method: 1.41424560546875
Number of iterations: 14
```
