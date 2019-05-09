---
title: "LIME (Local Interpretable Model-agnostic Explanations)"
tags:
  - LIME
  - explainableAI
  - EAI
  - ML
  - MachineLearning
use_math: true
permalink: '/XAI'
---

### Locality
모형 전부를 설명하진 못하지만, 각 instance의 근방(neighborhood)에 있는 데이터를 설명할 수 있는 간단한 모형을 찾는 것이 목적임.

### Fidelity-interpretability trade-off
Ribeiro et al.의 논문에서는 explanation을 $g:X'\rightarrow \mathbb{R}, g\in G$인 모형으로 정의한다. 
여기에서 $G$는 잠재적으로 해석이 가능한 모형의 집합(예를 들어, linear models, decision trees)이다. 
모든 $g$에 대해서 해석하기 쉬운 모형을 얻을 수는 없기 때문에, 
모형의 복잡도인 $\Omega(g)$를 정의하며 이는 interpretability와 대조된다. 
예를 들어, $\Omega(g)$는 Decision Tree, Linear modeld에서 각각 나무의 깊이, non-zero 계수의 개수를 의미한다.

$f: \mathbb{R}^{d}\rightarrow\mathbb{R}$, 즉 $f$는 학습한 모형으로 정의하자. 이어서 $\pi_{x}(z)$는 $x$와 $z$의 proximity에 대한 척도로 정의하고 나면 
explanation $g$를 찾기 위한 최적화 문제를 정의할 수 있다. 

$$
\xi(x)=\argmin_{g\in G}\mathcal{L}(f,g,\pi_{x})+\Omega(g)
$$

를 통하여 해석성(Interpretability)와 지역 신뢰성(local fidelity)를 모두 충족시키는 explanation $g$를 얻을 수 있으며, 얻어진 $g$를 
**LIME**이라고 부른다. 여기에서 $G$는 explanation families, $\mathcal{L}$은 fidelity functions, $\Omega$는 complexity measures이다.

