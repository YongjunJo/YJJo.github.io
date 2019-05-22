---
title: "LIME (Local Interpretable Model-agnostic Explanation)"
tags:
  - LIME
  - XAI
  - eXplainable AI
permalink: /XAI/lime
sidebar:
  nav: "docs"
use_math: true 
---

# Introduction
의사결정을 위해 다양한 분야에서 머신러닝 기법이 활용되기 시작했습니다.  하지만 머신러닝 모형 대부분은 블랙박스(black box)모형이기에 해석이 어렵다는 단점이 있습니다. 최근 이를 해석하기 위한 연구가 활발히 진행되고 있고, 본 내용은  "Why should i trust you?: Explaining the predictions of any classifier." 논문의 내용을 참조하여 작성되었습니다.

논문에서는 결과를 신뢰하는 것을  **예측결과 신뢰도**와 **모형 신뢰도**로 정의합니다. 예측결과를 신뢰(Trusting a prediction) 하는 것은 개별 예측결과를 믿고, 의사결정을 할 수 있는가에 대한 문제입니다. 예를 들어, 감기를 예측 하는 모형을 가정해보겠습니다. 어떤 사람의 진료결과를 모형에 넣었을 때, "감기 입니다." 라고 말해주는 것 보다는, 두통의 정도가 90이고 증상에 대한 점수가 -50일 때, 합이 40이니 "감기 입니다."라고 결론을 주는 것이 의사 결정에 큰 도움이 될 것입니다. 모형을 신뢰(Trusting a model)하는 것은  모형 자체를 믿을 수 있는가에 대한 문제입니다. 검증 데이터(validation data) 기반으로 모형을 평가하고, 주로 정확도(accuracy)를 이용하여 모형의 성능을 평가합니다. 하지만, 실 데이터에 적용해보면 정확도를 과대측정하는 경우가 많습니다.

![figure1](/assets/XAI/LIME/fig1.PNG){: .align-center}

논문에서는 두 가지 신뢰도 문제를 해결하기 위하여 두 방법을 제안합니다.

* **LIME**: 예측결과를 설명해주는 알고리즘으로 지역적으로 해석가능한 모형을 근사시켜줍니다. 
* **SP-LIME**: 모형의 신뢰도를 확인하기 위해, 결과를 대표할 수 있는 데이터 몇 가지를 선택해주는 알고리즘입니다.

# Local Interpretable Model-agnostic Explanation (LIME)

## Interpretable Data Representations
LIME을 설명하기 앞서 특성(feature)와 Interpretable data representations으로 구분짓겠습니다. Blackbox 모형을 해석하기 위해서는 사람이 해석할 수 있는 데이터 형태로 표현되어야 될 것 입니다. 즉 실제로 모형에 투입되는 특성(feature)와 별개로 인간이 해석할 수 있는 데이터 형태로 나타내는 것을 Interpretable Data Representations이라고 부릅니다.

**Interpretable Data Representation 예시**
1. 텍스트 데이터: 단어의 유무를 나타내는 binary vector. (모형에는 word embedding 결과 등이 feature로 이용될 수 있다.) 
2. 이미지 데이터: 이미지상 비슷한 부분(super-pixel, segmentation)의 유무를 나타내는 binary vector.
3. 테이블 데이터: 연속형의 경우 원데이터 상동, 범주형의 경우 각 변수의 수준별 유무를 나타내는 binary vector.

앞으로 $d$차원 데이터의 원데이터를 $x\in\mathbb{R}^{d}$로 나타내고, $x$에 대한 interpretable representation을 $x'\in\\{0, 1\\}^{d'}$로 나타내겠습니다.

## Fidelity-interpretability trade-off  
먼저 fidelity와 interpretability에 대해서 살펴보겠습니다.
### (Local) Fidelity
논문의 핵심 전제로, 데이터 공간의 전체에서 모형을 설명하는 것은 어려울지라도, **국소적인 데이터 공간(설명하고자 하는 데이터와 가까운 거리에 있는 공간)에서는 의미 있는 모형**으로 설명해낼 수 있다는 것입니다.
 %figure%
위 예제를 살펴 보겠습니다. 굵은 빨간 십자가가 설명하고자 하는 데이터 이고, 검은 점선은 explanation입니다. 검은 점선을 전체 모형의 관점으로 살펴보면 우측의 빨간 점 3개와 좌측의 파란 점 2개를 설명할 수 없습니다. 하지만 국소적인 공간에서 검은 점선을 살펴보면 완벽하게 빨간 십자가와 파란 점을 구분해 낼 수 있습니다.

### Interpretability
국소 공간의 explanation이 전체 공간을 설명할 수는 없습니다. 왜냐하면 국소적으로 중요한 feature가 전체 공간에서는 중요하지 않을 수 있고, 반대의 상황도 발생할 수 있기 때문입니다. 반면에 전체 공간의 explanation은 국소 공간을 설명할 수 있지만, 이는 **모형의 복잡성** 때문에 해석(interpretability)하기 어렵습니다.

따라서 local fidelity를 너무 신경쓰면 interpretability은 좋아지지만, global fidelity를 확신할 수 없고, global fidelity에 중점을 두면, local fidelity와 iterpretability이 떨어지게됩니다. 논문에서는 이를 Fidelity-Interpretability Trade-off라고 표현했습니다.  (trade-off라는 표현이 잘 와닿지는 않지만.. 아마 곧 이어질 Loss functions에서 interpretability를 complexity로 대신 반영시키기 때문에 이렇게 표현한듯 합니다..)

Interpretability와 Local fidelity 모두를 반영할 수 있는 explanation을 찾는 과정은 다음과 같이 요약할 수 있습니다.

* $f: \mathbb{R}^{d}\rightarrow\mathbb{R}$, $f$는 **학습한 모형**.
* $g:X'\rightarrow \mathbb{R}, g\in G$, Prediction에 대한 **Explanation**을 모형 $g$로 정의합니다. 여기에서 $G$는 잠재적으로 해석이 가능한 모형의 집합(예를 들어, linear models, decision trees)으로, 분석결과를 쉽게 설명할 수 있는 모든 모형을 말합니다.
* $\Omega(g)$ **모형의 복잡도**를 정의합니다. 이는 interpretability와 대조됩니다. 예를 들어, $\Omega(g)$는 Decision Tree, Linear model에서 각각 나무의 깊이, non-zero 계수의 개수를 의미합니다.
* $\pi_x(z)$ 유사도 측도 (proximity measure)를 정의합니다.  이는 **설명하고자하는 데이터 $x$와 다른 데이터 $z$간**의 유사도 측도입니다.

Interpretability와 Local fidelity를 모두 충족시키는 Explanation $g$를 찾기 위한 최적화 문제는   
$$  
\xi(x)=\underset{g\in G}{\operatorname{argmin}}\mathcal{L}(f,g,\pi_{x})+\Omega(g)  
$$ 
로 정의됩니다. 여기에서 $G$는 explanation families, $\mathcal{L}$은 fidelity functions, $\Omega$는 complexity measures이다. $\mathcal{L}$은 이어서 자세히 살펴 보겠습니다.
