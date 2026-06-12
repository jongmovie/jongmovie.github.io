---
title: "수치해석 정리 1: 유한차분법"
date: 2026-06-13 10:30:00 +0900
categories: ["수치해석", "유한차분법"]
tags: ["수치해석", "finite-difference", "PDE"]
math: true
---

유한차분법은 미분방정식을 격자 위의 차분식으로 근사하는 방법이다.

## 기본 아이디어

미분을 작은 간격 \( h \)에 대한 차분으로 근사한다.

\[
f'(x) \approx \frac{f(x+h)-f(x)}{h}
\]

## 금융공학에서의 활용

Black-Scholes PDE, Heston PDE 등 편미분방정식 기반 옵션 가격 계산에 사용할 수 있다.
