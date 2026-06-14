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
n\ge\frac{\ln{(b_0-a_0)}-\ln{\epsilon}}{\ln2}
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
n\ge\frac{\ln{(b_0-a_0)}-\ln{\epsilon}}{\ln2}
$$

# 성질 및 장단점

이분법의 주요 성질은 다음과 같다.

| 항목 | 내용 |
| --- | --- |
| 수렴 속도 | 느림 |
| 수렴 차수 | 1 |
| 필요한 초기값 | 2개, 즉 구간의 양 끝점 $a_0, b_0$ |
| 수렴 조건 | $f(a_0)f(b_0)<0$ |
| 미분값 | 필요 없음 |
| 중근의 경우 | 부호 변화가 없으면 찾기 어려움 |
| 고차원 문제 | 일반적인 형태로는 직접 확장하기 어려움 |

이분법의 장점은 개념이 단순하고 구현이 쉽다는 점이다.  
또한 조건 $f(a_0)f(b_0)<0$을 만족하는 초기 구간만 주어지면 근의 존재가 보장되며, 반복 과정에서 구간의 길이가 계속 줄어들기 때문에 안정적으로 근에 수렴한다.

그러나 이분법은 앞으로 배울 뉴턴 방법이나 할선법에 비해 수렴 속도가 느리다.  
또한 처음에 반드시 $f(a_0)f(b_0)<0$이 되는 구간을 찾아야 하며, 부호 변화가 없는 경우에는 적용하기 어렵다. 예를 들어 짝수 중근의 경우 함수값의 부호가 양쪽에서 바뀌지 않을 수 있으므로 이분법으로 근을 찾기 어렵다. 복소근 역시 실수 구간에서의 부호 변화를 이용하는 이분법으로는 찾을 수 없다.

초기 구간의 폭을 작게 잡을수록 필요한 반복 횟수는 줄어든다. 하지만 이 경우에도 반드시 그 구간 안에서 부호 변화가 일어나야 한다.

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
