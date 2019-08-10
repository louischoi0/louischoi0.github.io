---
layout: article 
title: "Spotify Recommendation System"
image:
  feature: "car.jpg"
  teaser: "car.jpg"
categories: ["ml"]
---

# 내 마음을 훔치는 스포티파이의 음악 추천 시스템 
 Recommanedation System With Logistic Matrix Factorization


[참조 문서](https://web.stanford.edu/~rezab/nips2014workshop/submits/logmat.pdf)
를 바탕으로 작성한 글입니다.

최근 몇년동안 추천 시스템, 개인 맞춤화 시스템에 대한 수요가 급증하고 이에 따른 기술적 발전이 급격하게 이루어 지고 있죠.   
수많은 알고리즘과 접근방법들에 대한 연구가 이루어지고 있는 가운데 글로벌 최대 음악 스트리밍 서비스사인 스포티파이의 음악 추천 시스템에 대해서 글을 써보고자 합니다 :D

개인 공부 및 정리 목적으로 쓴 글로, 내용상의 오류가 있을 수 있습니다.


## Implicit or Explicit ?

추천 시스템은 우선 데이터를 필요로 합니다. 유저 정보에 대한 데이터, 특히 유저의 행동(Interaction) 데이터를 기반으로 학습을 시키고 유저 취향에 맞는 아이템이 추천되도록 유도합니다.  
 시스템에서 수집할 수 있는 Interaction 데이터는 2가지의 특성으로 나뉩니다.  
 첫번째로 유저의 호불호가 확실하게 반영되는 데이터를 Explicit Feedback라 하는데, 
 예를 들면 유저들의 구매내역, 아이템에 대한 평가 내역 등이 있을 수 있습니다.   
 Explicit Feedback Data은 추천 시스템에 있어서 매우 귀중한 데이터 입니다. 하지만 그만큼 얻기 힘든 귀한 데이터이기도 합니다.  
 모든 유저들이 많은 아이템을 구매하거나, 댓글을 작성하는 슈퍼 유저들이 아니기도 하고, 가입한지 얼마 안된 유저들에게도 알맞은 추천을 해주어야 하기 때문입니다.

두번 째로 암시적인 유저들의 Interaction Data가 있는데 이를 Implicit Feedback Data 라 합니다.
예를 들면 유저들의 아이템 조회 정보나, 아이템 시청 정보 등이 있을 수 있는데,  
 이를 Implicit 이라 하는 이유는 시청정보 만으로는 유저가 그 아이템을 좋아하는지 아닌지를 알수 없기 때문입니다. 

Spotify 에서는 이러한 특성을 가진 Implicit Feedback Data를 이용한 대용량 트래픽이 발생하는 서비스내에서의 추천 시스템을 완벽하게 구현해내었습니다.

## Notation  

$$x = {-b \pm \sqrt{b^2-4ac} \over 2a}$$






$$
\begin{align*}
y = y(x,t) &= A e^{i\theta} \\
&= A (\cos \theta + i \sin \theta) \\
&= A (\cos(kx - \omega t) + i \sin(kx - \omega t)) \\
&= A\cos(kx - \omega t) + i A\sin(kx - \omega t)  \\
&= A\cos \Big(\frac{2\pi}{\lambda}x - \frac{2\pi v}{\lambda} t \Big) + i A\sin \Big(\frac{2\pi}{\lambda}x - \frac{2\pi v}{\lambda} t \Big)  \\
&= A\cos \frac{2\pi}{\lambda} (x - v t) + i A\sin \frac{2\pi}{\lambda} (x - v t)
\end{align*}
$$
