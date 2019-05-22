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

## Introduction
의사결정을 위해 다양한 분야에서 머신러닝 기법이 활용되기 시작했습니다.  하지만 머신러닝 모형 대부분은 블랙박스(black box)모형이기에 해석이 어렵다는 단점이 있습니다. 최근 이를 해석하기 위한 연구가 활발히 진행되고 있고, 본 내용은  "Why should i trust you?: Explaining the predictions of any classifier." 논문의 내용을 참조하여 작성되었습니다.

논문에서는 결과를 신뢰하는 것을  **예측결과 신뢰도**와 **모형 신뢰도**로 정의합니다. 예측결과를 신뢰(Trusting a prediction) 하는 것은 개별 예측결과를 믿고, 의사결정을 할 수 있는가에 대한 문제입니다. 예를 들어, 감기를 예측 하는 모형을 가정해보겠습니다. 어떤 사람의 진료결과를 모형에 넣었을 때, "감기 입니다." 라고 말해주는 것 보다는, 두통의 정도가 90이고 증상에 대한 점수가 -50일 때, 합이 40이니 "감기 입니다."라고 결론을 주는 것이 의사 결정에 큰 도움이 될 것입니다. 모형을 신뢰(Trusting a model)하는 것은  모형 자체를 믿을 수 있는가에 대한 문제입니다. 검증 데이터(validation data) 기반으로 모형을 평가하고, 주로 정확도(accuracy)를 이용하여 모형의 성능을 평가합니다. 하지만, 실 데이터에 적용해보면 정확도를 과대측정하는 경우가 많습니다.

![figure1](/assets/XAI/LIME/fig1.PNG){: .align-center}

논문에서는 두 가지 신뢰도 문제를 해결하기 위하여 두 방법을 제안합니다.

* **LIME**: 예측결과를 설명해주는 알고리즘으로 지역적으로 해석가능한 모형을 근사시켜줍니다. 
* **SP-LIME**: 모형의 신뢰도를 확인하기 위해, 결과를 대표할 수 있는 데이터 몇 가지를 선택해주는 알고리즘입니다.

---
# Local Interpretable Model-agnostic Explanation (LIME)

## Interpretable Data Representations
LIME을 설명하기 앞서 특성(feature)와 Interpretable data representations으로 구분짓겠습니다. Blackbox 모형을 해석하기 위해서는 사람이 해석할 수 있는 데이터 형태로 표현되어야 될 것 입니다. 즉 실제로 모형에 투입되는 특성(feature)와 별개로 인간이 해석할 수 있는 데이터 형태로 나타내는 것을 Interpretable Data Representations이라고 부릅니다.

**Interpretable Data Representation 예시**
1. 텍스트 데이터: 단어의 유무를 나타내는 binary vector. (모형에는 word embedding 결과 등이 feature로 이용될 수 있다.) 
2. 이미지 데이터: 이미지상 비슷한 부분(super-pixel, segmentation)의 유무를 나타내는 binary vector.
3. 테이블 데이터: 연속형의 경우 원데이터 상동, 범주형의 경우 각 변수의 수준별 유무를 나타내는 binary vector.

앞으로 $d$차원 데이터의 원데이터를 $x\in\mathbb{R}^{d}$로 나타내고, $x$에 대한 interpretable representation을 $x'\in\\{0, 1\\}^{d'}$로 나타내겠습니다.
## Locality  
모형 전부를 설명하진 못하지만, 각 instance의 근방(neighborhood)에 있는 데이터를 설명할 수 있는 간단한 모형을 찾는 것이 목적임.  
  
---
  
## Fidelity-interpretability trade-off  
Ribeiro et al.의 논문에서는 explanation을 $g:X'\rightarrow \mathbb{R}, g\in G$인 모형으로 정의한다.  
여기에서 $G$는 잠재적으로 해석이 가능한 모형의 집합(예를 들어, linear models, decision trees)이다.  
모든 $g$에 대해서 해석하기 쉬운 모형을 얻을 수는 없기 때문에,  
모형의 복잡도인 $\Omega(g)$를 정의하며 이는 interpretability와 대조된다.  
예를 들어, $\Omega(g)$는 Decision Tree, Linear modeld에서 각각 나무의 깊이, non-zero 계수의 개수를 의미한다.  
  
$f: \mathbb{R}^{d}\rightarrow\mathbb{R}$, 즉 $f$는 학습한 모형으로 정의하자. 이어서 $\pi_{x}(z)$는 $x$와 $z$의 proximity에 대한 척도로 정의하고 나면  
explanation $g$를 찾기 위한 최적화 문제를 정의할 수 있다.  
  
$$  
\xi(x)=\underset{g\in G}{\operatorname{argmin}}\mathcal{L}(f,g,\pi_{x})+\Omega(g)  
$$  
  
를 통하여 해석성(Interpretability)와 지역 신뢰성(local fidelity)를 모두 충족시키는 explanation $g$를 얻을 수 있으며, 얻어진 $g$를  
**LIME**이라고 부른다. 여기에서 $G$는 explanation families, $\mathcal{L}$은 fidelity functions, $\Omega$는 complexity measures이다.

## References
* Ribeiro, Marco Tulio, Sameer Singh, and Carlos Guestrin. "Why should i trust you?: Explaining the predictions of any classifier." _Proceedings of the 22nd ACM SIGKDD international conference on knowledge discovery and data mining_. ACM, 2016.
