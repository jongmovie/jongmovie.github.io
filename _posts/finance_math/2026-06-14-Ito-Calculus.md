---
title: "금융수학 3: 이토 적분(Ito Calculus)"
date: 2026-06-14 10:20:00 +0900
categories: ["금융수학 및 파생상품 모델", "금융수학"]
order: 3
math: true
---
---

# 이토 적분

확률미적분학을 이해하기 위해 꼭 알아야 할 개념 중 하나가 **이토 적분(Itô integral)**이다.
이토 적분은 매우 어렵고 복잡한 개념이지만, 여기서는 이후 전개에 필요한 수준에서 간단히 다루고자 한다.

이에 앞서 **키요시 이토(Kiyosi Itô, 1915--2008)**에 대해 살펴보자.
그는 브라운 운동과 같은 무작위적인 움직임을 수학적으로 다루기 위해 확률적분과 확률미분방정식의 기초를 마련한 수학자이다.

그의 연구는 오늘날 **이토 미적분학(Itô calculus)**으로 발전하였으며, 그 핵심 개념 중 하나가 바로 이토 적분이다.

이토 적분은 브라운 운동처럼 일반적인 의미에서 미분할 수 없는 과정에 대해서도 적분을 정의할 수 있게 해 준다.
따라서 이는 무작위성이 포함된 현상을 분석하기 위한 기본 도구이며, 이후 살펴볼 이토 보조정리와 확률미분방정식, 금융수학의 주가 모형 및 옵션 가격 결정 이론을 이해하는 데 중요한 기반이 된다.

---

## 1. 이토 적분의 정의

> **정의: 이토 적분**
>
> 닫힌구간 $[a,b]$를 균등하게 $n$등분하여 다음과 같이 정의하자.

$$
a=t\_0<t\_1<\cdots<t\_n=b,\qquad \Delta t=t\_k-t\_{k-1},\qquad \Delta B\_k=B\_{t\_k}-B\_{t\_{k-1}}
$$

다음과 같은 확률변수 $X\_n$을 정의한다.

$$
X\_n=\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k
$$

적절한 조건 아래에서 $X\_n$은 어떤 확률변수 $X$로 **평균제곱수렴(mean-square convergence)** 한다.

즉,

$$
\lim\_{n\to\infty}E\left[|X\_n-X|^2\right]=0
$$

이다.

이때 그 극한값 $X$를 다음과 같이 정의한다.

$$
X=\int\_a^b f(t,B\_t)\,dB\_t
$$

즉,

$$
\int\_a^b f(t,B\_t)\,dB\_t=\lim\_{n\to\infty}\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k
$$

로 정의하며, 이를 **이토 적분(Itô integral)**이라고 한다.

이토 적분에서 중요한 점은 각 구간의 **왼쪽 끝점 $t\_{k-1}$에서의 값**을 사용한다는 것이다.

시간에만 의존하는 함수 $f(t)$의 경우에는 평가 시점의 선택이 극한값에 큰 영향을 주지 않는다.
그러나 적분함수가 브라운 운동과 같은 확률과정에 의존하는 경우에는 각 구간에서 어느 시점의 값을 사용하느냐에 따라 확률적분합의 극한값이 달라질 수 있다.

이에 대해서는 뒤에서 브라운 운동의 이차변동을 살펴본 후 다시 설명한다.

---

## 2. 이토 적분의 평균과 이토 등거리성

이토 적분에는 다음과 같은 중요한 성질이 있다.

> **정리: 이토 적분의 평균과 이토 등거리성**

적절한 적분가능성 조건을 만족하는 함수 $f$에 대하여

$$
E\left[\int\_a^b f(t,B\_t)\,dB\_t\right]=0
$$

이 성립한다.

또한,

$$
E\left[\left(\int\_a^b f(t,B\_t)\,dB\_t\right)^2\right]=E\left[\int\_a^b f(t,B\_t)^2\,dt\right]
$$

가 성립한다.

두 번째 관계를 **이토 등거리성(Itô isometry)**이라고 한다.

### 증명

이토 적분의 근사합인 확률변수 $X\_n$을 다음과 같이 정의하자.

$$
X\_n=\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k
$$

먼저 $X\_n$의 기댓값을 계산한다.

기댓값의 선형성에 의해

$$
E[X\_n]=\sum\_{k=1}^n E\left[f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\right]
$$

이다.

각 $k$에 대해 시각 $t\_{k-1}$까지의 정보를 $\mathcal{F}\_{t\_{k-1}}$라고 하자.

그러면 $f(t\_{k-1},B\_{t\_{k-1}})$는 시각 $t\_{k-1}$까지의 정보로 결정된다.

따라서 타워 성질을 이용하면

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\right]=E\left[E\left[f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\mid\mathcal{F}\_{t\_{k-1}}\right]\right]
$$

이다.

$f(t\_{k-1},B\_{t\_{k-1}})$는 $\mathcal{F}\_{t\_{k-1}}$까지의 정보로 결정되므로 밖으로 꺼낼 수 있다.

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\right]=E\left[f(t\_{k-1},B\_{t\_{k-1}})E\left[\Delta B\_k\mid\mathcal{F}\_{t\_{k-1}}\right]\right]
$$

브라운 운동의 독립증분 성질에 의해 $\Delta B\_k$는 시각 $t\_{k-1}$까지의 정보와 독립이다.

또한,

$$
E[\Delta B\_k]=0
$$

이므로

$$
E\left[\Delta B\_k\mid\mathcal{F}\_{t\_{k-1}}\right]=0
$$

이다.

따라서

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\right]=0
$$

이다.

모든 항의 기댓값이 0이므로

$$
E[X\_n]=0
$$

을 얻는다.

이제 $n\to\infty$로 보내면

$$
X\_n\to\int\_a^b f(t,B\_t)\,dB\_t
$$

이므로

$$
E\left[\int\_a^b f(t,B\_t)\,dB\_t\right]=0
$$

을 얻는다.

---

다음으로 이토 등거리성을 살펴보자.

근사합을 제곱하면

$$
X\_n^2=\left(\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})\Delta B\_k\right)^2
$$

이다.

제곱합을 대각항과 교차항으로 나누면

$$
X\_n^2=\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2+2\sum\_{1\le i\lt j\le n}f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i\Delta B\_j
$$

이다.

따라서

$$
E[X\_n^2]=E\left[\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\right]+2E\left[\sum\_{1\le i\lt j\le n}f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i\Delta B\_j\right]
$$

이다.

먼저 첫 번째 항을 살펴보자.

각 $k$에 대하여 타워 성질을 이용하면

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\right]=E\left[E\left[f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\mid\mathcal{F}\_{t\_{k-1}}\right]\right]
$$

이다.

$f(t\_{k-1},B\_{t\_{k-1}})^2$를 밖으로 꺼내면

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\right]=E\left[f(t\_{k-1},B\_{t\_{k-1}})^2E\left[(\Delta B\_k)^2\mid\mathcal{F}\_{t\_{k-1}}\right]\right]
$$

이다.

브라운 운동의 증가분은

$$
\Delta B\_k\sim N(0,\Delta t)
$$

이므로

$$
E[(\Delta B\_k)^2]=\Delta t
$$

이다.

또한 $\Delta B\_k$는 $\mathcal{F}\_{t\_{k-1}}$와 독립이므로

$$
E\left[(\Delta B\_k)^2\mid\mathcal{F}\_{t\_{k-1}}\right]=\Delta t
$$

이다.

따라서

$$
E\left[f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\right]=E\left[f(t\_{k-1},B\_{t\_{k-1}})^2\Delta t\right]
$$

을 얻는다.

그러므로 첫 번째 항 전체는

$$
E\left[\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2(\Delta B\_k)^2\right]=E\left[\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2\Delta t\right]
$$

이 된다.

---

이제 교차항을 살펴보자.

$i<j$라고 하면

$$
f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i
$$

는 시각 $t\_{j-1}$까지의 정보로 결정된다.

따라서

$$
E\left[f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i\Delta B\_j\right]
$$

에 타워 성질을 적용하면

$$
E\left[f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i\Delta B\_j\right]=E\left[f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i E\left[\Delta B\_j\mid\mathcal{F}\_{t\_{j-1}}\right]\right]
$$

이다.

그런데

$$
E\left[\Delta B\_j\mid\mathcal{F}\_{t\_{j-1}}\right]=0
$$

이므로

$$
E\left[f(t\_{i-1},B\_{t\_{i-1}})f(t\_{j-1},B\_{t\_{j-1}})\Delta B\_i\Delta B\_j\right]=0
$$

이다.

따라서 모든 교차항의 기댓값이 사라지고

$$
E[X\_n^2]=E\left[\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2\Delta t\right]
$$

을 얻는다.

$n\to\infty$로 보내면 오른쪽의 합은 다음 적분으로 수렴한다.

$$
\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})^2\Delta t\to\int\_a^b f(t,B\_t)^2\,dt
$$

따라서

$$
E\left[\left(\int\_a^b f(t,B\_t)\,dB\_t\right)^2\right]=E\left[\int\_a^b f(t,B\_t)^2\,dt\right]
$$

을 얻는다.

이것이 **이토 등거리성(Itô isometry)**이다.

---

## 3. 브라운 운동의 이차변동과 이토 곱셈 규칙

이토 적분과 이토 보조정리를 이해하기 위해서는 브라운 운동의 중요한 성질인 **이차변동(quadratic variation)**을 먼저 살펴볼 필요가 있다.

고전 미적분에서는 작은 변화량 $dx$에 대하여 $(dx)^2$과 같은 고차항을 무시한다.

그러나 브라운 운동의 경우에는 상황이 다르다.

브라운 운동의 증가량 $\Delta B\_k$는 시간 간격 $\Delta t$에 대해 대략

$$
\sqrt{\Delta t}
$$

의 크기를 가진다.

따라서 그 제곱 $(\Delta B\_k)^2$은 $\Delta t$와 같은 크기를 가진다.

이 때문에 이토 계산에서는

$$
(dB\_t)^2=dt
$$

라는 특별한 계산 규칙이 나타난다.

---

### 브라운 운동의 이차변동

> **정리: 브라운 운동의 이차변동**
>
> 닫힌구간 $[0,T]$를 균등하게 $n$등분하여 다음과 같이 정의하자.

$$
0=t\_0<t\_1<\cdots<t\_n=T,\qquad \Delta t=t\_k-t\_{k-1},\qquad \Delta B\_k=B\_{t\_k}-B\_{t\_{k-1}}
$$

그러면

$$
\lim\_{n\to\infty}E\left[\left(\sum\_{k=1}^n(\Delta B\_k)^2-T\right)^2\right]=0
$$

이 성립한다.

즉,

$$
\sum\_{k=1}^n(\Delta B\_k)^2\to T
$$

로 평균제곱수렴한다.

### 증명

브라운 운동의 증가분에 대해

$$
\Delta B\_k\sim N(0,\Delta t)
$$

이므로 표준정규확률변수 $Z\_k\sim N(0,1)$를 이용하여

$$
\Delta B\_k=\sqrt{\Delta t}Z\_k
$$

라고 쓸 수 있다.

따라서

$$
E[(\Delta B\_k)^2]=\Delta t
$$

이다.

표준정규확률변수의 4차 적률은

$$
E[Z\_k^4]=3
$$

이므로

$$
E[(\Delta B\_k)^4]=E[(\sqrt{\Delta t}Z\_k)^4]=3(\Delta t)^2
$$

이다.

따라서

$$
\mathrm{Var}[(\Delta B\_k)^2]=E[(\Delta B\_k)^4]-\left(E[(\Delta B\_k)^2]\right)^2
$$

이고,

$$
\mathrm{Var}[(\Delta B\_k)^2]=3(\Delta t)^2-(\Delta t)^2=2(\Delta t)^2
$$

이다.

브라운 운동의 서로 다른 구간의 증가분들은 서로 독립이므로

$$
\mathrm{Var}\left[\sum\_{k=1}^n(\Delta B\_k)^2\right]=\sum\_{k=1}^n\mathrm{Var}[(\Delta B\_k)^2]
$$

이다.

따라서

$$
\mathrm{Var}\left[\sum\_{k=1}^n(\Delta B\_k)^2\right]=2n(\Delta t)^2
$$

이다.

균등분할에서는

$$
\Delta t=\frac{T}{n}
$$

이므로

$$
2n(\Delta t)^2=2n\left(\frac{T}{n}\right)^2=\frac{2T^2}{n}
$$

이다.

또한

$$
E\left[\sum\_{k=1}^n(\Delta B\_k)^2\right]=\sum\_{k=1}^nE[(\Delta B\_k)^2]
$$

이고,

$$
\sum\_{k=1}^nE[(\Delta B\_k)^2]=\sum\_{k=1}^n\Delta t=T
$$

이다.

따라서

$$
E\left[\left(\sum\_{k=1}^n(\Delta B\_k)^2-T\right)^2\right]=\mathrm{Var}\left[\sum\_{k=1}^n(\Delta B\_k)^2\right]
$$

이고,

$$
E\left[\left(\sum\_{k=1}^n(\Delta B\_k)^2-T\right)^2\right]=\frac{2T^2}{n}
$$

이다.

$n\to\infty$로 보내면

$$
\lim\_{n\to\infty}E\left[\left(\sum\_{k=1}^n(\Delta B\_k)^2-T\right)^2\right]=0
$$

을 얻는다.

따라서

$$
\sum\_{k=1}^n(\Delta B\_k)^2\to T
$$

로 평균제곱수렴한다.

---

## 4. 이토 곱셈 규칙

앞에서 살펴본 브라운 운동의 이차변동을 간단히 쓰면

$$
(dB\_t)^2=dt
$$

가 된다.

여기서 $(dB\_t)^2=dt$는 엄밀한 의미의 일반적인 미분 등식이 아니라, 브라운 운동의 이차변동을 나타내는 형식적인 표현이다.

브라운 운동의 증가량은 대략

$$
dB\_t\sim\sqrt{dt}
$$

의 크기를 가진다.

따라서

$$
(dB\_t)^2\sim dt
$$

이다.

반면 $dt\,dB\_t$는 대략

$$
dt\,dB\_t\sim(dt)^{3/2}
$$

의 크기를 가지므로 $dt$보다 높은 차수의 항이다.

또한 $(dt)^2$ 역시 $dt$보다 높은 차수의 항이다.

따라서 이토 계산에서는 다음과 같은 곱셈 규칙을 사용한다.

$$
(dB\_t)^2=dt,\qquad dt\,dB\_t=0,\qquad (dt)^2=0
$$

이 세 관계를 **이토 곱셈 규칙(Itô multiplication rules)**이라고 하며, 이토 보조정리에서 핵심적인 역할을 한다.

| 곱 | 결과 |
| --- | --- |
| $dt\cdot dt$ | $0$ |
| $dt\cdot dB\_t$ | $0$ |
| $dB\_t\cdot dt$ | $0$ |
| $dB\_t\cdot dB\_t$ | $dt$ |

---

## 5. 평가 시점에 따른 확률적분합의 차이

이토 적분의 정의에서 왜 왼쪽 끝점 $t\_{k-1}$의 값을 사용하는지가 중요하다는 점을 살펴보자.

리만 적분에서는 보통 구간 안의 어느 점을 선택하더라도 같은 극한값을 얻는다.

그러나 확률적분에서는 적분함수가 브라운 운동에 의존하는 경우 평가 시점에 따라 극한값이 달라질 수 있다.

이를 직접 계산으로 확인해보자.

### 5.1 왼쪽 끝점을 사용하는 경우

다음 확률적분합을 생각하자.

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k
$$

브라운 운동의 증가량 정의에 의해

$$
B\_{t\_k}=B\_{t\_{k-1}}+\Delta B\_k
$$

이다.

양변을 제곱하면

$$
B\_{t\_k}^2=B\_{t\_{k-1}}^2+2B\_{t\_{k-1}}\Delta B\_k+(\Delta B\_k)^2
$$

이다.

따라서

$$
2B\_{t\_{k-1}}\Delta B\_k=B\_{t\_k}^2-B\_{t\_{k-1}}^2-(\Delta B\_k)^2
$$

이고,

$$
B\_{t\_{k-1}}\Delta B\_k=\frac{1}{2}\left(B\_{t\_k}^2-B\_{t\_{k-1}}^2\right)-\frac{1}{2}(\Delta B\_k)^2
$$

이다.

이를 $k=1,\ldots,n$까지 모두 더하면

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k=\frac{1}{2}\sum\_{k=1}^n\left(B\_{t\_k}^2-B\_{t\_{k-1}}^2\right)-\frac{1}{2}\sum\_{k=1}^n(\Delta B\_k)^2
$$

이다.

첫 번째 합은 망원합이므로

$$
\sum\_{k=1}^n\left(B\_{t\_k}^2-B\_{t\_{k-1}}^2\right)=B\_T^2-B\_0^2
$$

이다.

일반적으로 $B\_0=0$이므로

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k=\frac{1}{2}B\_T^2-\frac{1}{2}\sum\_{k=1}^n(\Delta B\_k)^2
$$

이다.

앞에서 살펴본 이차변동에 의해

$$
\sum\_{k=1}^n(\Delta B\_k)^2\to T
$$

이므로

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k\to\frac{1}{2}(B\_T^2-T)
$$

이다.

따라서 이토 적분은

$$
\int\_0^T B\_t\,dB\_t=\frac{1}{2}(B\_T^2-T)
$$

가 된다.

---

### 5.2 오른쪽 끝점을 사용하는 경우

이번에는 각 구간의 오른쪽 끝점 $t\_k$에서 값을 평가하자.

$$
\sum\_{k=1}^nB\_{t\_k}\Delta B\_k
$$

여기서

$$
B\_{t\_k}=B\_{t\_{k-1}}+\Delta B\_k
$$

이므로

$$
\sum\_{k=1}^nB\_{t\_k}\Delta B\_k=\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k+\sum\_{k=1}^n(\Delta B\_k)^2
$$

이다.

앞에서

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k\to\frac{1}{2}(B\_T^2-T)
$$

임을 확인하였다.

또한

$$
\sum\_{k=1}^n(\Delta B\_k)^2\to T
$$

이므로

$$
\sum\_{k=1}^nB\_{t\_k}\Delta B\_k\to\frac{1}{2}(B\_T^2-T)+T
$$

이다.

따라서

$$
\sum\_{k=1}^nB\_{t\_k}\Delta B\_k\to\frac{1}{2}(B\_T^2+T)
$$

을 얻는다.

---

## 6. 왼쪽 끝점과 오른쪽 끝점의 비교

왼쪽 끝점을 사용하는 경우에는

$$
\sum\_{k=1}^nB\_{t\_{k-1}}\Delta B\_k\to\frac{1}{2}(B\_T^2-T)
$$

이다.

반면 오른쪽 끝점을 사용하는 경우에는

$$
\sum\_{k=1}^nB\_{t\_k}\Delta B\_k\to\frac{1}{2}(B\_T^2+T)
$$

이다.

즉, 두 극한값은 서로 다르며 그 차이는

$$
T
$$

이다.

따라서 브라운 운동을 적분하는 경우에는 **각 구간의 어느 시점에서 적분함수의 값을 평가하느냐에 따라 극한값이 달라진다.**

이토 적분에서는 각 구간의 왼쪽 끝점 $t\_{k-1}$의 값을 사용한다.

이는 $f(t\_{k-1},B\_{t\_{k-1}})$가 시각 $t\_{k-1}$까지 관측된 정보만을 사용하기 때문이다.

반면 오른쪽 끝점의 값 $B\_{t\_k}$에는 현재 구간에서 새롭게 발생한 증가량 $\Delta B\_k$의 정보까지 포함되어 있다.

따라서 앞으로 사용하는 이토 적분은 다음과 같이 왼쪽 끝점을 사용하는 확률적분으로 정의한다.

$$
\int\_a^b f(t,B\_t)\,dB\_t=\lim\_{n\to\infty}\sum\_{k=1}^n f(t\_{k-1},B\_{t\_{k-1}})(B\_{t\_k}-B\_{t\_{k-1}})
$$

---

## 7. 정리

이토 적분은 브라운 운동과 같이 일반적인 의미에서 미분할 수 없는 확률과정을 다루기 위해 정의되는 확률적분이다.

이토 적분은 각 구간의 왼쪽 끝점에서 적분함수를 평가하며,

$$
E\left[\int\_a^b f(t,B\_t)\,dB\_t\right]=0
$$

이라는 성질을 가진다.

또한 이토 등거리성에 의해

$$
E\left[\left(\int\_a^b f(t,B\_t)\,dB\_t\right)^2\right]=E\left[\int\_a^b f(t,B\_t)^2\,dt\right]
$$

이 성립한다.

브라운 운동의 이차변동은

$$
\sum\_{k=1}^n(\Delta B\_k)^2\to T
$$

이며, 이를 형식적으로 표현하면

$$
(dB\_t)^2=dt
$$

가 된다.

따라서 이토 미적분에서는

$$
(dB\_t)^2=dt,\qquad dt\,dB\_t=0,\qquad (dt)^2=0
$$

이라는 곱셈 규칙을 사용한다.

특히,

$$
\int\_0^T B\_t\,dB\_t=\frac{1}{2}(B\_T^2-T)
$$

이다.

이 결과는 일반적인 미적분에서 예상할 수 있는 $\frac{1}{2}B\_T^2$와 차이가 있다.

그 차이는 바로 브라운 운동의 이차변동에서 발생하며, 이후 살펴볼 **이토 보조정리에서 2차 미분항이 사라지지 않는 이유**이기도 하다.

---
