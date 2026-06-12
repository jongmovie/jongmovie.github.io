---
title: "(모델링 1) Black-Scholes 모델"
date: 2026-06-12 10:10:00 +0900
categories: [금융수학, 파생상품 모델링]
tags: [파생상품, Black-Scholes, 옵션가격, 위험중립측도]
math: true
---

파생상품 모델링은 기초자산의 확률적 움직임을 가정하고, 그 위에서 옵션과 같은 상품의 가격을 계산하는 과정이다.

## Black-Scholes 모델

Black-Scholes 모델에서는 기초자산 가격 $$ S_t $$가 다음 확률미분방정식을 따른다고 가정한다.

$$
dS_t = \mu S_t dt + \sigma S_t dW_t
$$

여기서 $$ \mu $$는 기대수익률, $$ \sigma $$는 변동성, $$ W_t $$는 브라운 운동이다.

## 핵심 아이디어

옵션 가격은 위험중립측도 하에서 미래 payoff의 할인기댓값으로 계산된다.

$$
V_0 = e^{-rT} \mathbb{E}^{\mathbb{Q}}[\mathrm{Payoff}(S_T)]
$$
