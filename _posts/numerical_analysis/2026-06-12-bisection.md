---
title: "비선형 방정식의 해법 1: 이분법(Bisection Method)"
date: 2026-06-12 10:30:00 +0900
categories: ["수치해석", "비선형 방정식의 해법"]
tags: ["수치해석", "비선형방정식", "이분법"]
order: 1
math: true
---

# 이분법 Bisection Method 정리

이분법은 구간 $[a_0,b_0]$에서 연속인 함수 $f(x)$의 근을 찾는 대표적인 수치해석 방법이다.
이 방법은 **중간값 정리**를 기반으로 하며, 근이 존재하는 구간을 반복적으로 절반씩 줄여가면서 근의 근삿값을 구한다.

---

## 1. 이론적 배경

이분법의 핵심 원리는 중간값 정리이다.

> **중간값 정리**
>
> 함수 $f:[a,b]\to \mathbb{R}$가 폐구간 $[a,b]$에서 연속이고
> $f(a)f(b)<0$이면,
>
> $f(c)=0$
>
> 을 만족하는 점 $c\in(a,b)$가 적어도 하나 존재한다.

즉, 연속 함수의 양 끝점에서 함수값의 부호가 서로 다르면, 그 구간 내부에는 반드시 적어도 하나의 근이 존재한다.
이분법은 이 성질을 이용하여 근이 포함된 구간을 계속 절반으로 나누며 해를 좁혀가는 방법이다.

---

## 2. 이분법 알고리즘

이분법의 절차는 다음과 같다.

1. $f(a_0)f(b_0)<0$을 만족하는 초기 구간 $[a_0,b_0]$을 선택한다.

2. 구간의 중간점 $m_0$을 계산한다.

$$
m_0=\frac{a_0+b_0}{2}
$$

3. 중간점에서의 함수값을 이용하여 근이 포함된 구간을 갱신한다.

   * $f(a_0)f(m_0)<0$이면

$$
a_1=a_0,\qquad b_1=m_0
$$

* $f(m_0)f(b_0)<0$이면

$$
a_1=m_0,\qquad b_1=b_0
$$

4. 다음 조건 중 하나를 만족할 때까지 위 과정을 반복한다.

$$
|b_n-a_n|<\epsilon
$$

또는

$$
|f(m_n)|<\epsilon
$$

여기서 $\epsilon$은 허용 오차이다.

---

## 3. 수렴성

함수 $f(x)$가 폐구간 $[a_0,b_0]$에서 연속이고,

$$
f(a_0)f(b_0)<0
$$

을 만족한다고 하자. 이때 이분법으로 생성되는 중간점 수열 ${m_n}$은 실제 근 $p$에 수렴한다.

이분법에서 $n$번째 반복 후 중간점 $m_n$과 실제 근 $p$ 사이의 오차는 다음과 같이 제한된다.

$$
|m_n-p|\le\frac{1}{2^{n+1}}(b_0-a_0)
$$

따라서 반복 횟수가 증가할수록 오차는 $2$의 거듭제곱 비율로 감소한다.

---

## 4. 이분법의 반복 횟수

이분법으로 허용 오차 $\epsilon$ 이내의 근을 찾기 위해서는 적어도 다음 부등식을 만족하는 반복 횟수 $n$이 필요하다.

$$
n\ge\frac{\ln{(b_0-a_0)}-\ln{\epsilon}}{\ln2}-1
$$

### 증명

이분법에서는 매 반복마다 근을 포함하는 구간의 길이가 절반으로 줄어든다. 따라서 $n$번째 반복 후 구간의 길이는 다음과 같다.

$$
\begin{aligned}
b_n-a_n
&=\frac{1}{2}(b_{n-1}-a_{n-1}) \
&=\frac{1}{2^n}(b_0-a_0)
\end{aligned}
$$

실제 근을 $p$, 중간점을

$$
m_n=\frac{a_n+b_n}{2}
$$

라고 하면, 근 $p$는 항상 구간 $[a_n,b_n]$ 안에 존재하므로 다음 부등식이 성립한다.

$$
\begin{aligned}
|m_n-p|
&\le m_n-a_n \
&=\frac{1}{2}(b_n-a_n) \
&=\frac{1}{2^{n+1}}(b_0-a_0)
\end{aligned}
$$

이 값이 허용 오차 $\epsilon$보다 작거나 같아야 하므로,

$$
\frac{1}{2^{n+1}}(b_0-a_0)\le \epsilon
$$

이다. 양변에 로그를 취하면 다음을 얻는다.

$$
n\ge\frac{\ln(b_0-a_0)-\ln\epsilon}{\ln 2}-1
$$


---

## 5. 이분법의 성질

이분법의 주요 성질은 다음과 같이 정리할 수 있다.

| 항목      | 내용                        |
| ------- | ------------------------- |
| 수렴 속도   | 느림                        |
| 수렴 차수   | 1                         |
| 필요한 초기값 | 2개, 즉 구간의 양 끝점 $a_0, b_0$ |
| 수렴 조건   | $f(a_0)f(b_0)<0$          |
| 미분값     | 필요 없음                     |
| 중근의 경우  | 부호 변화가 없으면 찾기 어려움         |
| 고차원 문제  | 일반적인 형태로는 직접 확장하기 어려움     |

---

## 6. 장점과 단점

### 장점

이분법의 가장 큰 장점은 개념이 단순하고 구현이 쉽다는 점이다.
또한 초기 구간이

$$
f(a_0)f(b_0)<0
$$

을 만족하기만 하면 중간값 정리에 의해 근의 존재가 보장된다.

반복 과정에서도 근을 포함하는 구간이 계속 유지되며, 구간의 길이가 절반씩 줄어들기 때문에 매우 안정적으로 근에 수렴한다.
따라서 이분법은 수치해석에서 가장 기본적이면서도 신뢰할 수 있는 근 찾기 방법 중 하나이다.

### 단점

반면 이분법은 뉴턴 방법이나 할선법에 비해 수렴 속도가 느리다.
또한 처음에 반드시 $f(a_0)f(b_0)<0$이 되는 구간을 찾아야 한다.

부호 변화가 없는 경우에는 적용하기 어렵다. 예를 들어 짝수 중근의 경우, 근의 양쪽에서 함수값의 부호가 바뀌지 않을 수 있으므로 이분법으로 근을 찾기 어렵다.

또한 이분법은 실수 구간에서의 부호 변화를 이용하는 방법이므로, 복소근을 직접 찾는 데에는 적합하지 않다.

초기 구간의 폭을 작게 잡으면 필요한 반복 횟수는 줄어든다. 하지만 이 경우에도 반드시 해당 구간 안에서 부호 변화가 발생해야 한다.

---

## 7. 파이썬 구현

다음은 함수 $f(x)=x^2-2$의 근을 이분법으로 구하는 파이썬 코드이다.

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

---

## 8. 실행 결과

![Bisection Method Graph](/assets/images/Bisection_method.png)

```text
Approximate root by bisection method: 1.41424560546875
Number of iterations: 14
```

위 결과에서 이분법은 구간 $[0,2]$에서 시작하여 $x^2-2=0$의 양의 근을 근사적으로 계산한다.
실제 근은 다음과 같다.

$$
\sqrt{2}\approx 1.41421356
$$

코드에서 얻은 근삿값은 다음과 같다.

$$
1.41424560546875
$$

이는 허용 오차 $0.0001$ 범위 내에서 실제 값과 매우 가깝다.

---

## 9. 정리

이분법은 연속 함수의 부호 변화를 이용하여 근을 찾는 안정적인 수치해석 방법이다.
초기 구간만 적절히 선택하면 근의 존재가 보장되고, 반복할수록 오차가 확실하게 감소한다.

다만 수렴 속도가 상대적으로 느리며, 부호 변화가 없는 근이나 복소근을 찾는 데에는 한계가 있다.
그럼에도 불구하고 이분법은 구현이 쉽고 안정성이 높기 때문에 수치해석에서 가장 먼저 배우는 기본적인 근 찾기 알고리즘으로 널리 사용된다.
