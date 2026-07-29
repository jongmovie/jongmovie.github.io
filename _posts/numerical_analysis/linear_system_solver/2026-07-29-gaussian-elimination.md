---
title: "선형 시스템 풀이1. 가우스 소거법"
date: 2026-07-29 10:00:00 +0900
categories: ["수치해석", "선형 시스템 풀이"]
tags: ["수치해석", "가우스 소거법","gausian elimination"]
order: 1
math: true
---

# 선형연립방정식

선형연립방정식은 여러 개의 미지수에 관한 일차방정식들을 동시에 만족하는 해를 구하는 문제이다.

미지수가 $n$개이고 방정식도 $n$개인 선형연립방정식을 생각하자.

$$
\begin{aligned}
a_{11}x_1+a_{12}x_2+\cdots+a_{1n}x_n &= b_1,\\
a_{21}x_1+a_{22}x_2+\cdots+a_{2n}x_n &= b_2,\\
&\vdots\\
a_{n1}x_1+a_{n2}x_2+\cdots+a_{nn}x_n &= b_n.
\end{aligned}
$$

여기서

- $x_1,x_2,\ldots,x_n$은 구하고자 하는 $n$개의 미지수이다.
- 각 방정식은 미지수들이 만족해야 하는 하나의 제약조건이다.
- $a_{ij}$는 $i$번째 방정식에서 $x_j$에 곱해지는 계수이다.
- $b_i$는 $i$번째 방정식의 우변에 주어진 값이다.

계수행렬 $A$는 다음과 같다.

$$
A=
\begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1n}\\
a_{21} & a_{22} & \cdots & a_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
a_{n1} & a_{n2} & \cdots & a_{nn}
\end{bmatrix}
\in\mathbb{R}^{n\times n}.
$$

미지수 벡터와 우변 벡터는 각각 다음과 같다.

$$
\mathbf{x}=
\begin{bmatrix}
x_1\\
x_2\\
\vdots\\
x_{n-1}\\
x_n
\end{bmatrix}
\in\mathbb{R}^{n},
\qquad
\mathbf{b}=
\begin{bmatrix}
b_1\\
b_2\\
\vdots\\
b_{n-1}\\
b_n
\end{bmatrix}
\in\mathbb{R}^{n}.
$$

즉 행렬로 위 선형연립방정식을 간단하게 표현하면 다음과 같다.

$$
A\mathbf{x}=\mathbf{b}
$$

앞으로 우리는 위와 같은 선형연립방정식 해법에 대해 알아볼예정이다. 우선 가장 기본이며 간단한 가우스 소거법에 대해 알아보자.

## 가우스 소거법

가우스 소거법은 선형연립방정식

$$
A\mathbf{x}=\mathbf{b}
$$

를 행 연산을 이용하여 상삼각 선형시스템으로 변환한 다음, 후진 대입을 통해 해를 구하는 직접법이다.

다음과 같은 $n$개의 선형연립방정식을 생각하자.

$$
\begin{aligned}
a_{11}x_1+a_{12}x_2+\cdots+a_{1n}x_n&=b_1,\\
a_{21}x_1+a_{22}x_2+\cdots+a_{2n}x_n&=b_2,\\
&\vdots\\
a_{n1}x_1+a_{n2}x_2+\cdots+a_{nn}x_n&=b_n.
\end{aligned}
$$

이를 행렬 형태로 나타내면 다음과 같다.

$$
\begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1n}\\
a_{21} & a_{22} & \cdots & a_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
a_{n1} & a_{n2} & \cdots & a_{nn}
\end{bmatrix}
\begin{bmatrix}
x_1\\
x_2\\
\vdots\\
x_n
\end{bmatrix}
=\begin{bmatrix}
b_1\\
b_2\\
\vdots\\
b_n
\end{bmatrix}.
$$

가우스 소거법은 다음 두 단계로 구성된다.

1. 소거 단계를 통해 계수행렬을 상삼각행렬로 변환한다.
2. 후진 대입을 통해 $x_n,x_{n-1},\ldots,x_1$을 계산한다.

---

## 확대행렬

선형시스템의 계수행렬과 우변 벡터를 하나의 확대행렬로 나타낼 수 있다.

$$
\widetilde{A}=
\left[
\begin{array}{cccc|c}
a_{11} & a_{12} & \cdots & a_{1n} & b_1\\
a_{21} & a_{22} & \cdots & a_{2n} & b_2\\
\vdots & \vdots & \ddots & \vdots & \vdots\\
a_{n1} & a_{n2} & \cdots & a_{nn} & b_n
\end{array}
\right].
$$

우변 벡터를 확대행렬의 $(n+1)$번째 열로 생각하면 다음과 같이 쓸 수 있다.

$$
a_{i,n+1}=b_i,
\qquad
i=1,2,\ldots,n.
$$

따라서 확대행렬은 다음과 같이 표현된다.

$$
\widetilde{A}=
\left[
\begin{array}{cccc|c}
a_{11} & a_{12} & \cdots & a_{1n} & a_{1,n+1}\\
a_{21} & a_{22} & \cdots & a_{2n} & a_{2,n+1}\\
\vdots & \vdots & \ddots & \vdots & \vdots\\
a_{n1} & a_{n2} & \cdots & a_{nn} & a_{n,n+1}
\end{array}
\right].
$$

가우스 소거법에서는 기본 행 연산을 사용하여 주대각선 아래의 원소를 차례로 $0$으로 만든다.

소거 과정에서 사용하는 행 연산은 다음과 같다.

$$
R_i\leftarrow R_i-l_{ik}R_k.
$$

여기서 $R_k$는 피벗 행이고, $R_i$는 소거하려는 행이다. $l_{ik}$는 소거 계수 또는 승수라고 한다.

---

## 소거 단계

$k$번째 단계에서는 $a_{kk}$를 피벗으로 사용하여 다음 원소를 모두 $0$으로 만든다.

$$
a_{k+1,k},a_{k+2,k},\ldots,a_{nk}.
$$

$i$번째 행의 $k$번째 원소를 제거하기 위한 소거 계수는 다음과 같다.

$$
l_{ik}=\frac{a_{ik}}{a_{kk}}.
$$

이 소거 계수를 이용하여 다음 행 연산을 수행한다.

$$
R_i\leftarrow R_i-l_{ik}R_k.
$$

계수행렬과 우변을 포함한 확대행렬의 원소는 다음과 같이 갱신된다.

$$
a_{ij}\leftarrow a_{ij}-l_{ik}a_{kj},
\qquad
j=k+1,k+2,\ldots,n+1.
$$

여기서 $j=n+1$인 경우에는 우변 벡터가 갱신된다.

$$
a_{i,n+1}\leftarrow a_{i,n+1}-l_{ik}a_{k,n+1}.
$$

### 소거 단계 알고리즘

```text
for k = 1, 2, ..., n-1
    for i = k+1, k+2, ..., n
        l[i][k] = a[i][k] / a[k][k]
        a[i][k] = 0

        for j = k+1, k+2, ..., n+1
            a[i][j] = a[i][j] - l[i][k] * a[k][j]
```

이를 수식 형태로 나타내면 다음과 같다.

$$
\begin{aligned}
&\text{for }k=1,2,\ldots,n-1\\
&\qquad \text{for }i=k+1,k+2,\ldots,n\\
&\qquad\qquad l_{ik}\leftarrow\frac{a_{ik}}{a_{kk}}\\
&\qquad\qquad a_{ik}\leftarrow 0\\
&\qquad\qquad \text{for }j=k+1,k+2,\ldots,n+1\\
&\qquad\qquad\qquad a_{ij}\leftarrow a_{ij}-l_{ik}a_{kj}
\end{aligned}
$$

소거 단계가 완료되면 확대행렬은 다음과 같은 상삼각 형태가 된다.

$$
\left[
\begin{array}{ccccc|c}
a_{11} & a_{12} & a_{13} & \cdots & a_{1n} & a_{1,n+1}\\
0 & a_{22} & a_{23} & \cdots & a_{2n} & a_{2,n+1}\\
0 & 0 & a_{33} & \cdots & a_{3n} & a_{3,n+1}\\
\vdots & \vdots & \ddots & \ddots & \vdots & \vdots\\
0 & 0 & \cdots & 0 & a_{nn} & a_{n,n+1}
\end{array}
\right].
$$

따라서 소거가 완료된 선형시스템은 다음과 같다.

$$
\begin{aligned}
a_{11}x_1+a_{12}x_2+\cdots+a_{1n}x_n&=a_{1,n+1},\\
a_{22}x_2+\cdots+a_{2n}x_n&=a_{2,n+1},\\
&\vdots\\
a_{n-1,n-1}x_{n-1}+a_{n-1,n}x_n&=a_{n-1,n+1},\\
a_{nn}x_n&=a_{n,n+1}.
\end{aligned}
$$

---

## 후진 대입 단계

상삼각 선형시스템의 $i$번째 방정식은 다음과 같다.

$$
a_{ii}x_i+\sum_{j=i+1}^{n}a_{ij}x_j=a_{i,n+1}.
$$

따라서 $x_i$는 다음과 같이 계산된다.

$$
x_i=
\frac{
a_{i,n+1}-\displaystyle\sum_{j=i+1}^{n}a_{ij}x_j
}{
a_{ii}
}.
$$

마지막 방정식에서는 합이 존재하지 않으므로 다음을 얻는다.

$$
x_n=\frac{a_{n,n+1}}{a_{nn}}.
$$

그다음 $x_{n-1},x_{n-2},\ldots,x_1$을 역순으로 계산한다.

### 후진 대입 알고리즘

```text
for i = n, n-1, ..., 1
    tmp = 0

    for j = i+1, i+2, ..., n
        tmp = tmp + a[i][j] * x[j]

    x[i] = (a[i][n+1] - tmp) / a[i][i]
```

이를 수식 형태로 나타내면 다음과 같다.

$$
\begin{aligned}
&\text{for }i=n,n-1,\ldots,1\\
&\qquad tmp\leftarrow 0\\
&\qquad \text{for }j=i+1,i+2,\ldots,n\\
&\qquad\qquad tmp\leftarrow tmp+a_{ij}x_j\\
&\qquad x_i\leftarrow\frac{a_{i,n+1}-tmp}{a_{ii}}
\end{aligned}
$$

---

## 전체 알고리즘

```text
입력: 확대행렬 a
출력: 해 벡터 x

소거 단계

for k = 1, 2, ..., n-1
    for i = k+1, k+2, ..., n
        l[i][k] = a[i][k] / a[k][k]
        a[i][k] = 0

        for j = k+1, k+2, ..., n+1
            a[i][j] = a[i][j] - l[i][k] * a[k][j]

후진 대입 단계

for i = n, n-1, ..., 1
    tmp = 0

    for j = i+1, i+2, ..., n
        tmp = tmp + a[i][j] * x[j]

    x[i] = (a[i][n+1] - tmp) / a[i][i]
```

이 알고리즘은 모든 피벗 $a_{kk}$가 $0$이 아니라고 가정한다.

---

## 연산 횟수

연산 횟수는 알고리즘이 수행하는 곱셈, 나눗셈, 덧셈, 뺄셈 등의 기본 산술 연산이 입력 크기 $n$에 따라 몇 번 수행되는지를 나타낸다.

여기서는 다음 두 종류의 연산을 구분하여 계산한다.

- 곱셈과 나눗셈: $*$, $/$
- 덧셈과 뺄셈: $+$, $-$

대입 연산, 반복문 조건 확인, 인덱스 증가와 같은 연산은 주요 산술 연산 횟수에서 제외한다.

---

## 소거 단계의 곱셈과 나눗셈 횟수

$k$번째 소거 단계에서 $i$ 반복문의 실행 횟수는 다음과 같다.

$$
n-k.
$$

각 $i$에 대하여 소거 계수

$$
l_{ik}=\frac{a_{ik}}{a_{kk}}
$$

를 계산할 때 나눗셈이 한 번 수행된다.

또한 내부 반복문은

$$
j=k+1,k+2,\ldots,n+1
$$

의 범위를 가지므로 총 $n-k+1$번 실행된다.

각 반복에서 다음 곱셈이 한 번 수행된다.

$$
l_{ik}a_{kj}.
$$

따라서 고정된 $k$에 대하여 각 행에서 수행되는 곱셈과 나눗셈 횟수는 다음과 같다.

$$
1+(n-k+1)=n-k+2.
$$

$i$ 반복문은 $n-k$번 실행되므로 $k$번째 단계의 곱셈과 나눗셈 횟수는 다음과 같다.

$$
(n-k)(n-k+2).
$$

따라서 소거 단계의 전체 곱셈과 나눗셈 횟수는 다음과 같다.

$$
N_{\mathrm{elim}}^{(*,/)}
=\sum_{k=1}^{n-1}(n-k)(n-k+2).
$$

$r=n-k$로 치환하면 다음을 얻는다.

$$
\begin{aligned}
N_{\mathrm{elim}}^{(*,/)}
&=\sum_{r=1}^{n-1}r(r+2)\\
&=\sum_{r=1}^{n-1}r^2+2\sum_{r=1}^{n-1}r\\
&=\frac{(n-1)n(2n-1)}{6}+(n-1)n\\
&=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n.
\end{aligned}
$$

따라서 소거 단계의 곱셈과 나눗셈 횟수는 정확히 다음과 같다.

$$
\boxed{
N_{\mathrm{elim}}^{(*,/)}
=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n
}
$$

최고차항은 $\frac{1}{3}n^3$이므로 소거 단계의 계산량은 $n^3$에 비례하여 증가한다.

---

## 소거 단계의 덧셈과 뺄셈 횟수

행렬 원소의 갱신식은 다음과 같다.

$$
a_{ij}\leftarrow a_{ij}-l_{ik}a_{kj}.
$$

각 갱신에서 뺄셈이 한 번 수행된다.

고정된 $k$에서 $i$ 반복문은 $n-k$번, $j$ 반복문은 $n-k+1$번 실행된다.

따라서 소거 단계의 덧셈과 뺄셈 횟수는 다음과 같다.

$$
N_{\mathrm{elim}}^{(+,-)}
=\sum_{k=1}^{n-1}(n-k)(n-k+1).
$$

$r=n-k$로 치환하면 다음과 같다.

$$
\begin{aligned}
N_{\mathrm{elim}}^{(+,-)}
&=\sum_{r=1}^{n-1}r(r+1)\\
&=\sum_{r=1}^{n-1}r^2+\sum_{r=1}^{n-1}r\\
&=\frac{(n-1)n(2n-1)}{6}+\frac{(n-1)n}{2}\\
&=\frac{1}{3}n^3-\frac{1}{3}n.
\end{aligned}
$$

따라서

$$
\boxed{
N_{\mathrm{elim}}^{(+,-)}
=\frac{1}{3}n^3-\frac{1}{3}n
}
$$

이다.

---

## 후진 대입의 곱셈과 나눗셈 횟수

$i$번째 미지수를 계산할 때 다음 합을 계산한다.

$$
\sum_{j=i+1}^{n}a_{ij}x_j.
$$

$i$번째 단계에서 곱셈은 $n-i$번 수행되고, 마지막에 $a_{ii}$로 나누는 나눗셈이 한 번 수행된다.

따라서 $i$번째 단계의 곱셈과 나눗셈 횟수는 다음과 같다.

$$
n-i+1.
$$

전체 곱셈과 나눗셈 횟수는 다음과 같다.

$$
N_{\mathrm{back}}^{(*,/)}
=\sum_{i=1}^{n}(n-i+1).
$$

$k=n-i+1$로 치환하면 다음과 같다.

$$
\begin{aligned}
N_{\mathrm{back}}^{(*,/)}
&=\sum_{k=1}^{n}k\\
&=\frac{n(n+1)}{2}\\
&=\frac{1}{2}n^2+\frac{1}{2}n.
\end{aligned}
$$

따라서

$$
\boxed{
N_{\mathrm{back}}^{(*,/)}
=\frac{1}{2}n^2+\frac{1}{2}n
}
$$

이다.

---

## 후진 대입의 덧셈과 뺄셈 횟수

후진 대입에서 합을 구성하기 위해 필요한 덧셈과 뺄셈 횟수는 다음과 같다.

$$
N_{\mathrm{back}}^{(+,-)}
=\sum_{i=1}^{n}(n-i).
$$

이를 계산하면 다음과 같다.

$$
\begin{aligned}
N_{\mathrm{back}}^{(+,-)}
&=\sum_{k=1}^{n-1}k\\
&=\frac{n(n-1)}{2}\\
&=\frac{1}{2}n^2-\frac{1}{2}n.
\end{aligned}
$$

따라서

$$
\boxed{
N_{\mathrm{back}}^{(+,-)}
=\frac{1}{2}n^2-\frac{1}{2}n
}
$$

이다.

이 횟수는 우변에서 항들을 차례로 빼는 다음과 같은 동치 구현을 기준으로 한다.

```text
tmp = a[i][n+1]

for j = i+1, i+2, ..., n
    tmp = tmp - a[i][j] * x[j]

x[i] = tmp / a[i][i]
```

`tmp=0`으로 시작하고 마지막에 `a[i][n+1]-tmp`를 계산하는 구현을 문자 그대로 세면 뺄셈이 추가로 발생할 수 있다. 그러나 두 구현의 차이는 최대 $O(n)$이며 전체 시간복잡도에는 영향을 주지 않는다.

---

## 전체 연산 횟수

### 전체 곱셈과 나눗셈 횟수

소거 단계와 후진 대입 단계의 곱셈 및 나눗셈 횟수를 더하면 다음과 같다.

$$
N_{\mathrm{total}}^{(*,/)}
=N_{\mathrm{elim}}^{(*,/)}+N_{\mathrm{back}}^{(*,/)}.
$$

따라서

$$
\begin{aligned}
N_{\mathrm{total}}^{(*,/)}
&=\left(
\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n
\right)
+\left(
\frac{1}{2}n^2+\frac{1}{2}n
\right)\\
&=\frac{1}{3}n^3+n^2-\frac{1}{3}n.
\end{aligned}
$$

즉,

$$
\boxed{
N_{\mathrm{total}}^{(*,/)}
=\frac{1}{3}n^3+n^2-\frac{1}{3}n
}
$$

이다.

### 전체 덧셈과 뺄셈 횟수

덧셈과 뺄셈 횟수는 다음과 같다.

$$
N_{\mathrm{total}}^{(+,-)}
=N_{\mathrm{elim}}^{(+,-)}+N_{\mathrm{back}}^{(+,-)}.
$$

따라서

$$
\begin{aligned}
N_{\mathrm{total}}^{(+,-)}
&=\left(
\frac{1}{3}n^3-\frac{1}{3}n
\right)
+\left(
\frac{1}{2}n^2-\frac{1}{2}n
\right)\\
&=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n.
\end{aligned}
$$

즉,

$$
\boxed{
N_{\mathrm{total}}^{(+,-)}
=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n
}
$$

이다.

---

## 연산 횟수 예시

위의 공식에 $n$을 대입하면 다음과 같은 연산 횟수를 얻는다.

| $n$ | 곱셈과 나눗셈 $(*,/)$ | 덧셈과 뺄셈 $(+,-)$ |
|---:|---:|---:|
| $3$ | $17$ | $11$ |
| $10$ | $430$ | $375$ |
| $50$ | $44\,150$ | $42\,875$ |
| $100$ | $343\,300$ | $338\,250$ |

예를 들어 $n=10$인 경우 곱셈과 나눗셈 횟수는 다음과 같다.

$$
\begin{aligned}
N_{\mathrm{total}}^{(*,/)}
&=\frac{1}{3}(10)^3+(10)^2-\frac{1}{3}(10)\\
&=430.
\end{aligned}
$$

덧셈과 뺄셈 횟수는 다음과 같다.

$$
\begin{aligned}
N_{\mathrm{total}}^{(+,-)}
&=\frac{1}{3}(10)^3+\frac{1}{2}(10)^2-\frac{5}{6}(10)\\
&=375.
\end{aligned}
$$

---

## 시간복잡도의 정의

시간복잡도는 입력 크기가 증가할 때 알고리즘의 연산량이 어떤 비율로 증가하는지를 나타낸다.

가우스 소거법에서 입력 크기는 선형시스템의 차원 $n$이다. 실제 실행시간은 컴퓨터의 성능, 프로그래밍 언어, 컴파일러 및 메모리 구조에 따라 달라질 수 있으므로, 시간복잡도에서는 실제 초 단위 실행시간보다 연산 횟수의 증가율을 분석한다.

알고리즘의 연산 횟수를 $T(n)$이라고 하자.

### Big-O 표기법

양의 상수 $C$와 자연수 $n_0$가 존재하여 모든 $n\geq n_0$에 대하여

$$
0\leq T(n)\leq Cg(n)
$$

이 성립하면 다음과 같이 쓴다.

$$
T(n)=O(g(n)).
$$

Big-O는 알고리즘 연산량의 점근적인 상한을 나타낸다.

예를 들어

$$
T(n)=\frac{1}{3}n^3+n^2-\frac{1}{3}n
$$

이라고 하자. $n$이 충분히 커지면 $n^3$ 항이 $n^2$ 및 $n$ 항보다 빠르게 증가하여 전체 연산량을 지배한다.

따라서

$$
T(n)=O(n^3)
$$

이라고 쓴다.

상수배는 증가 차수에 영향을 주지 않으므로 다음 두 표기는 동일한 시간복잡도를 나타낸다.

$$
O\left(\frac{1}{3}n^3\right)=O(n^3).
$$

마찬가지로

$$
O\left(\frac{1}{2}n^2\right)=O(n^2).
$$

따라서 $O(n^3/3)$ 또는 $O(n^2/2)$보다 각각 $O(n^3)$과 $O(n^2)$으로 표현하는 것이 일반적이다.

---

## Big-O와 정확한 연산 횟수의 차이

다음 식은 가우스 소거법에서 수행되는 곱셈과 나눗셈의 정확한 횟수를 나타낸다.

$$
N_{\mathrm{total}}^{(*,/)}
=\frac{1}{3}n^3+n^2-\frac{1}{3}n.
$$

반면 다음 식은 연산량의 증가 차수만 나타낸다.

$$
N_{\mathrm{total}}^{(*,/)}=O(n^3).
$$

즉, 정확한 연산 횟수는 계수와 낮은 차수의 항을 모두 포함하지만, Big-O 시간복잡도는 $n$이 매우 커졌을 때 지배적인 최고차항만 고려한다.

---

## 소거 단계의 시간복잡도

소거 단계의 곱셈과 나눗셈 횟수는 다음과 같다.

$$
N_{\mathrm{elim}}^{(*,/)}
=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n.
$$

최고차항은 $\frac{1}{3}n^3$이므로 소거 단계의 시간복잡도는 다음과 같다.

$$
T_{\mathrm{elim}}(n)=O(n^3).
$$

더 정확하게는 연산량이 위아래 모두 $n^3$에 비례하므로 다음과 같이 쓸 수 있다.

$$
T_{\mathrm{elim}}(n)=\Theta(n^3).
$$

---

## 후진 대입의 시간복잡도

후진 대입의 곱셈과 나눗셈 횟수는 다음과 같다.

$$
N_{\mathrm{back}}^{(*,/)}
=\frac{1}{2}n^2+\frac{1}{2}n.
$$

최고차항은 $\frac{1}{2}n^2$이므로 후진 대입의 시간복잡도는 다음과 같다.

$$
T_{\mathrm{back}}(n)=O(n^2).
$$

더 정확하게는 다음과 같다.

$$
T_{\mathrm{back}}(n)=\Theta(n^2).
$$

---

## 전체 시간복잡도

가우스 소거법의 전체 연산량은 소거 단계와 후진 대입 단계의 합이다.

$$
T(n)=T_{\mathrm{elim}}(n)+T_{\mathrm{back}}(n).
$$

따라서

$$
T(n)=O(n^3)+O(n^2).
$$

$n$이 충분히 커지면 $n^3$이 $n^2$보다 빠르게 증가하므로 전체 시간복잡도는 다음과 같다.

$$
\boxed{
T(n)=O(n^3)
}
$$

정확한 점근적 증가 차수는 다음과 같다.

$$
\boxed{
T(n)=\Theta(n^3)
}
$$

즉, 가우스 소거법에서는 후진 대입보다 소거 단계의 연산량이 전체 실행시간을 지배한다.

---

## 부분 피벗팅

기본 가우스 소거법은 각 단계의 피벗 $a_{kk}$가 $0$이 아니라고 가정한다.

그러나 피벗이 $0$이거나 매우 작은 경우에는 소거 계수

$$
l_{ik}=\frac{a_{ik}}{a_{kk}}
$$

가 정의되지 않거나 절댓값이 매우 커질 수 있다. 이 경우 반올림오차가 크게 증폭될 수 있다.

이를 방지하기 위해 실제 계산에서는 부분 피벗팅을 사용한다.

$k$번째 단계에서 다음 조건을 만족하는 행 번호 $p$를 선택한다.

$$
p=\underset{k\leq i\leq n}{\arg\max}\left|a_{ik}\right|.
$$

그다음 $k$번째 행과 $p$번째 행을 교환한다.

$$
R_k\leftrightarrow R_p.
$$

부분 피벗팅에서는 각 열에서 최대 절댓값을 찾기 위한 비교 연산과 행 교환 연산이 추가된다. 이러한 추가 연산량은 대략 $O(n^2)$이므로 가우스 소거법의 전체 시간복잡도는 여전히 다음과 같다.

$$
O(n^3).
$$

---

## 정리

가우스 소거법의 전체 과정은 다음과 같다.

$$
A\mathbf{x}=\mathbf{b}
$$

$$
\Downarrow\quad\text{소거 단계}
$$

$$
U\mathbf{x}=\mathbf{c}
$$

$$
\Downarrow\quad\text{후진 대입}
$$

$$
\mathbf{x}=
\begin{bmatrix}
x_1\\
x_2\\
\vdots\\
x_n
\end{bmatrix}.
$$

각 단계의 시간복잡도는 다음과 같다.

$$
\boxed{
\text{소거 단계}:O(n^3),
\qquad
\text{후진 대입}:O(n^2),
\qquad
\text{전체}:O(n^3)
}
$$

가우스 소거법의 전체 곱셈과 나눗셈 횟수는 다음과 같다.

$$
\boxed{
N_{\mathrm{total}}^{(*,/)}
=\frac{1}{3}n^3+n^2-\frac{1}{3}n
}
$$

전체 덧셈과 뺄셈 횟수는 다음과 같다.

$$
\boxed{
N_{\mathrm{total}}^{(+,-)}
=\frac{1}{3}n^3+\frac{1}{2}n^2-\frac{5}{6}n
}
$$

$n$이 충분히 클 때 후진 대입의 $O(n^2)$ 연산보다 전진 소거의 $O(n^3)$ 연산이 전체 실행시간을 지배한다.

---

