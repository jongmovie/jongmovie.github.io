---
title: "선형 시스템 풀이1. 가우스 소거법"
date: 2026-07-30 10:00:00 +0900
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
\end{bmatrix}=
\begin{bmatrix}
b_1\\
b_2\\
\vdots\\
b_n
\end{bmatrix}.
$$

가우스 소거법은 다음 두 단계로 구성된다.

1. 전진 소거를 통해 계수행렬을 상삼각행렬로 변환한다.
2. 후진 대입을 통해 미지수 $x_n,x_{n-1},\ldots,x_1$을 계산한다.

---

## 확대행렬

선형시스템 $A\mathbf{x}=\mathbf{b}$는 다음과 같은 확대행렬로 나타낼 수 있다.

$$
\left[
\begin{array}{cccc|c}
a_{11} & a_{12} & \cdots & a_{1n} & b_1\\
a_{21} & a_{22} & \cdots & a_{2n} & b_2\\
\vdots & \vdots & \ddots & \vdots & \vdots\\
a_{n1} & a_{n2} & \cdots & a_{nn} & b_n
\end{array}
\right].
$$

가우스 소거법에서는 기본 행 연산을 사용하여 주대각선 아래의 원소를 차례로 $0$으로 만든다.

기본 행 연산은 다음과 같다.

- 두 행의 위치를 교환한다.
- 하나의 행에 $0$이 아닌 상수를 곱한다.
- 하나의 행에 다른 행의 상수배를 더한다.

가우스 소거법의 소거 과정에서는 주로 다음 행 연산을 사용한다.

$$
R_i\leftarrow R_i-m_{ik}R_k.
$$

여기서 $R_k$는 피벗 행이고, $R_i$는 소거하려는 행이다. $m_{ik}$는 $i$번째 행의 $k$번째 원소를 제거하기 위한 소거 계수이다.

---

## 전진 소거

첫 번째 열에서 $a_{11}$을 피벗으로 사용하여 $a_{21},a_{31},\ldots,a_{n1}$을 모두 $0$으로 만든다.

$i$번째 행의 첫 번째 원소를 제거하기 위한 소거 계수는 다음과 같다.

$$
m_{i1}=\frac{a_{i1}}{a_{11}},
\qquad
i=2,3,\ldots,n.
$$

이때 수행하는 행 연산은 다음과 같다.

$$
R_i\leftarrow R_i-m_{i1}R_1.
$$

첫 번째 소거 단계가 끝나면 확대행렬은 다음과 같은 형태가 된다.

$$
\left[
\begin{array}{ccccc|c}
a_{11}^{(1)} & a_{12}^{(1)} & a_{13}^{(1)} & \cdots & a_{1n}^{(1)} & b_1^{(1)}\\
0 & a_{22}^{(2)} & a_{23}^{(2)} & \cdots & a_{2n}^{(2)} & b_2^{(2)}\\
0 & a_{32}^{(2)} & a_{33}^{(2)} & \cdots & a_{3n}^{(2)} & b_3^{(2)}\\
\vdots & \vdots & \vdots & \ddots & \vdots & \vdots\\
0 & a_{n2}^{(2)} & a_{n3}^{(2)} & \cdots & a_{nn}^{(2)} & b_n^{(2)}
\end{array}
\right].
$$

이제 두 번째 열에서는 $a_{22}^{(2)}$를 피벗으로 사용하여 그 아래의 원소를 제거한다.

일반적으로 $k$번째 소거 단계에서 피벗은 $a_{kk}^{(k)}$이고, $i$번째 행의 소거 계수는 다음과 같다.

$$
m_{ik}=\frac{a_{ik}^{(k)}}{a_{kk}^{(k)}},
\qquad
i=k+1,k+2,\ldots,n.
$$

이 소거 계수를 이용하여 다음 행 연산을 수행한다.

$$
R_i\leftarrow R_i-m_{ik}R_k.
$$

따라서 계수행렬의 원소는 다음과 같이 갱신된다.

$$
a_{ij}^{(k+1)}=
a_{ij}^{(k)}-m_{ik}a_{kj}^{(k)},
\qquad
j=k,k+1,\ldots,n.
$$

우변 벡터의 원소도 동일한 행 연산에 따라 다음과 같이 갱신된다.

$$
b_i^{(k+1)}=
b_i^{(k)}-m_{ik}b_k^{(k)}.
$$

특히 $j=k$를 대입하면 다음을 얻는다.

$$
\begin{aligned}
a_{ik}^{(k+1)}
&=a_{ik}^{(k)}-m_{ik}a_{kk}^{(k)}\\
&=a_{ik}^{(k)}
-\frac{a_{ik}^{(k)}}{a_{kk}^{(k)}}a_{kk}^{(k)}\\
&=0.
\end{aligned}
$$

따라서 $k$번째 소거 단계가 끝나면 $k$번째 열에서 피벗 아래에 있는 모든 원소가 $0$이 된다.

이 과정을 $k=1,2,\ldots,n-1$에 대해 반복하면 다음과 같은 상삼각 선형시스템을 얻는다.

$$
U\mathbf{x}=\mathbf{c}.
$$

여기서 상삼각행렬 $U$는 다음과 같다.

$$
U=
\begin{bmatrix}
u_{11} & u_{12} & u_{13} & \cdots & u_{1n}\\
0 & u_{22} & u_{23} & \cdots & u_{2n}\\
0 & 0 & u_{33} & \cdots & u_{3n}\\
\vdots & \vdots & \ddots & \ddots & \vdots\\
0 & 0 & \cdots & 0 & u_{nn}
\end{bmatrix}.
$$

소거 과정에 의해 변환된 우변 벡터는 다음과 같다.

$$
\mathbf{c}=
\begin{bmatrix}
c_1\\
c_2\\
\vdots\\
c_n
\end{bmatrix}.
$$

따라서 소거가 완료된 선형시스템은 다음과 같이 전개된다.

$$
\begin{aligned}
u_{11}x_1+u_{12}x_2+\cdots+u_{1n}x_n&=c_1,\\
u_{22}x_2+\cdots+u_{2n}x_n&=c_2,\\
&\vdots\\
u_{n-1,n-1}x_{n-1}+u_{n-1,n}x_n&=c_{n-1},\\
u_{nn}x_n&=c_n.
\end{aligned}
$$

---

## 후진 대입

상삼각 선형시스템의 마지막 방정식은 다음과 같다.

$$
u_{nn}x_n=c_n.
$$

따라서 $x_n$은 다음과 같이 계산된다.

$$
x_n=\frac{c_n}{u_{nn}}.
$$

그다음 $x_n$을 바로 위의 방정식에 대입하면 $x_{n-1}$을 구할 수 있다.

$$
u_{n-1,n-1}x_{n-1}+u_{n-1,n}x_n=c_{n-1}.
$$

따라서

$$
x_{n-1}=
\frac{c_{n-1}-u_{n-1,n}x_n}{u_{n-1,n-1}}.
$$

일반적으로 $i$번째 방정식은 다음과 같다.

$$
u_{ii}x_i+\sum_{j=i+1}^{n}u_{ij}x_j=c_i.
$$

따라서 $x_i$는 다음 공식으로 계산된다.

$$
x_i=
\frac{
c_i-\displaystyle\sum_{j=i+1}^{n}u_{ij}x_j
}{
u_{ii}
},
\qquad
i=n-1,n-2,\ldots,1.
$$

후진 대입 공식은 다음과 같이 정리할 수 있다.

$$
x_n=\frac{c_n}{u_{nn}},
$$

$$
x_i=
\frac{
c_i-\displaystyle\sum_{j=i+1}^{n}u_{ij}x_j
}{
u_{ii}
},
\qquad
i=n-1,n-2,\ldots,1.
$$

---

## 가우스 소거법 알고리즘

입력은 계수행렬 $A\in\mathbb{R}^{n\times n}$과 우변 벡터 $\mathbf{b}\in\mathbb{R}^{n}$이고, 출력은 선형시스템의 해 $\mathbf{x}$이다.

### 1단계: 전진 소거

$$
\begin{aligned}
&\text{for }k=1,2,\ldots,n-1\\
&\qquad \text{for }i=k+1,k+2,\ldots,n\\
&\qquad\qquad m_{ik}\leftarrow\frac{a_{ik}}{a_{kk}}\\
&\qquad\qquad \text{for }j=k+1,k+2,\ldots,n\\
&\qquad\qquad\qquad a_{ij}\leftarrow a_{ij}-m_{ik}a_{kj}\\
&\qquad\qquad b_i\leftarrow b_i-m_{ik}b_k\\
&\qquad\qquad a_{ik}\leftarrow 0
\end{aligned}
$$

### 2단계: 후진 대입

먼저 마지막 미지수 $x_n$을 계산한다.

$$
x_n\leftarrow\frac{b_n}{a_{nn}}.
$$

그다음 나머지 미지수를 역순으로 계산한다.

$$
\begin{aligned}
&\text{for }i=n-1,n-2,\ldots,1\\
&\qquad s\leftarrow 0\\
&\qquad \text{for }j=i+1,i+2,\ldots,n\\
&\qquad\qquad s\leftarrow s+a_{ij}x_j\\
&\qquad x_i\leftarrow\frac{b_i-s}{a_{ii}}
\end{aligned}
$$

전체 알고리즘을 의사코드로 나타내면 다음과 같다.

```text
입력: A, b
출력: x

전진 소거

for k = 1, 2, ..., n-1
    for i = k+1, k+2, ..., n
        m = A[i][k] / A[k][k]

        for j = k+1, k+2, ..., n
            A[i][j] = A[i][j] - m * A[k][j]

        b[i] = b[i] - m * b[k]
        A[i][k] = 0

후진 대입

x[n] = b[n] / A[n][n]

for i = n-1, n-2, ..., 1
    sum = 0

    for j = i+1, i+2, ..., n
        sum = sum + A[i][j] * x[j]

    x[i] = (b[i] - sum) / A[i][i]
```

---

## 부분 피벗팅

기본 가우스 소거법에서는 각 단계의 피벗 $a_{kk}$가 $0$이 아니어야 한다.

그러나 피벗이 $0$이거나 매우 작은 경우에는 소거 계수

$$
m_{ik}=\frac{a_{ik}}{a_{kk}}
$$

의 절댓값이 매우 커질 수 있다. 이 경우 반올림오차가 크게 증가하여 계산 결과가 부정확해질 수 있다.

이를 방지하기 위해 부분 피벗팅을 사용한다.

$k$번째 소거 단계에서 $k$번째 열의 원소 중 절댓값이 가장 큰 원소가 있는 행 번호를 선택한다.

$$
p=
\underset{k\leq i\leq n}{\arg\max}
\left|a_{ik}\right|.
$$

그다음 $k$번째 행과 $p$번째 행을 교환한다.

$$
R_k\leftrightarrow R_p.
$$

행렬 $A$의 행을 교환할 때 우변 벡터 $\mathbf{b}$의 해당 원소도 함께 교환해야 한다.

$$
a_{kj}\leftrightarrow a_{pj},
\qquad
j=1,2,\ldots,n,
$$

$$
b_k\leftrightarrow b_p.
$$

부분 피벗팅을 포함한 전진 소거 알고리즘은 다음과 같다.

```text
for k = 1, 2, ..., n-1
    p = k

    for i = k+1, k+2, ..., n
        if abs(A[i][k]) > abs(A[p][k])
            p = i

    if abs(A[p][k]) is sufficiently small
        행렬이 특이행렬이거나 특이행렬에 매우 가깝다

    A의 k번째 행과 p번째 행을 교환
    b[k]와 b[p]를 교환

    for i = k+1, k+2, ..., n
        m = A[i][k] / A[k][k]

        for j = k+1, k+2, ..., n
            A[i][j] = A[i][j] - m * A[k][j]

        b[i] = b[i] - m * b[k]
        A[i][k] = 0
```

실제 수치계산에서는 기본 가우스 소거법보다 부분 피벗팅을 포함한 가우스 소거법을 사용하는 것이 일반적이다.

---

## 계산 복잡도

전진 소거 단계에서는 세 개의 반복문이 사용되므로 계산 복잡도는 다음과 같다.

$$
O(n^3).
$$

후진 대입 단계의 계산 복잡도는 다음과 같다.

$$
O(n^2).
$$

따라서 전체 가우스 소거법의 계산 복잡도는 다음과 같다.

$$
O(n^3).
$$

전진 소거에 필요한 주요 부동소수점 연산 횟수는 대략 다음과 같다.

$$
\frac{2}{3}n^3.
$$

$n$이 충분히 클 때 후진 대입의 $O(n^2)$ 연산보다 전진 소거의 $O(n^3)$ 연산이 전체 실행시간을 지배한다.

---

