---

layout: article

title: "내 마음을 훔치는 스포티파이 음악 추천 시스템 - 1"

image:

  teaser:car.jpg

  feature:car.jpg

category: "ml"
---

### Recommendation System With LMF(Logistic Matrix Factorization)



최근 몇년동안 추천 시스템, 개인 맞춤화 시스템에 대한 수요가 급증하고 이에 따른 기술적 발전이 급격하게 이루어 지고 있죠.

수많은 알고리즘과 접근방법들에 대한 연구가 이루어지고 있는 가운데 글로벌 최대 음악 스트리밍 서비스사인 스포티파이의 음악 추천 시스템에 대해서 글을 써보고자 합니다 :D

[참조 문서](https://web.stanford.edu/~rezab/nips2014workshop/submits/logmat.pdf) 를 바탕으로 작성한 글입니다.

최대한 초심자들을 대상으로 쉽게 쉽게 풀어 쓰는것이 최종 목표인 문서 입니다 ~~




## Implicit or Explicit ?

  

추천 시스템은 우선 데이터를 필요로 합니다. 유저 정보에 대한 데이터, 특히 유저의 행동(Interaction) 데이터를 기반으로 학습을 시키고 유저 취향에 맞는 아이템이 추천되도록 유도합니다.

시스템에서 수집할 수 있는 Interaction 데이터는 2가지의 특성으로 나뉩니다.첫번째로 유저의 호불호가 확실하게 반영되는 데이터를 Explicit Feedback Data라 하는데,예를 들면 유저들의 구매내역, 아이템에 대한 평가 내역 등이 있을 수 있습니다.

Explicit Feedback Data은 추천 시스템에 있어서 매우 귀중한 데이터 입니다. 하지만 그만큼 얻기 힘든 귀한 데이터이기도 합니다. 

Explicit Feedback Data로 추천 모델을 만들지 않은 이유는 아마도 모든 유저들이 많은 아이템을 구매하거나, 댓글을 작성하는 슈퍼 유저들이 아니기도 하고, 가입한지 얼마 안된 유저들에게도 알맞은 추천을 해주어야 하기 때문이기 아닐까 개인적으로 생각합니다.

두번 째로 암시적인 유저들의 Interaction Data가 있는데 이를 Implicit Feedback Data 라 합니다.예를 들면 유저들의 아이템 조회 정보나, 아이템 시청 정보 등이 있습니다.
 이를 Implicit 이라 하는 이유는 시청정보 만으로는 유저가 그 아이템을 좋아하는지 아닌지를 판별할 수 없기 때문입니다.

  

Spotify 에서는 이러한 특성을 가진 Implicit Feedback Data를 이용한 대용량 트래픽이 발생하는 서비스내에서의 추천 시스템을 완벽하게 구현해내었습니다.

  

## Notation


$$
\mathcal{U(i)} :\; A\;Group\;Of\;User\\
\mathcal{I(i)} :\; A\;Group\;Of\;Item\\
\mathcal{R(u,i)} :\; A\;Number\;Of\;Atrraction\;Of\;User\;i\; To\; Item\;i
$$


대부분의 머신 러닝 모델들은 잡다한 수학식으로 이루어진 짬뽕요리인건 모두들 아실거라 생각합니다 ^^;



여기서 U는 유저 i에 대한 Factor Vector가 로우로 이루어진 행렬이고,  I는 아이템 i에 대한 행렬입니다.

Factor Vector란 각 Feature들에대한 가중치를 가지고 있는 벡터인데, Factor 개수는 설정하기 나름입니다. 일반적으로 Factor 개수가 많으면 많을 수록 손실함수의 평가값은 낮아지는 경향이 있습니다.



R은 유저 i가 아이템 i에 대해 Interaction이 발생한 횟수를 가진 행렬 입니다. 만약 유저 A가 아이템 B을 2번 청취하였으면, 이에 대한 값은 2가 됩니다.



간단히 말해 Spotify의 Recommendation System은 아래 수식을 만족 시키는 U와 I행렬을 찾는 문제로 볼 수 있습니다.


$$
\mathbf{U}\bullet\;\; \mathbf{I}^T =\;\mathbf{R}
$$


따라서, 손실 함수는 대략적으로 아래와 같이 정의 될 수 있고, 이를 최소화 시키는 문제로 치환 됩니다.


$$
L\;=\;\mathbf{U}\bullet\;\; \mathbf{I}^T -\;\mathbf{R}\;\\
Loss \;= \; \sum_{}\mathbf{L}(u,i)
$$


물론 Loss 계산시 저 수식 그대로 계산하지는 않습니다. Loss가 양수일수도, 음수일수도 있어 단순히 합산하는 방법으로는 제대로 Loss를 구할수 없기 때문에 오차 합산하기전, MSE와 같은 함수를 적용합니다.

 

예를 들어 유저 A,B,C가 있고, Feature로는 아이템에 대한 장르,발매지역,가수에 대한 가중치를 가지는 벡터로 설정한다면 U행렬은 아래와 같을수 있습니다.



$$\begin{Bmatrix}
0.9 & -0.8 & 0.7 \\
0.3 & 0.3 & 0.4 \\ 0.6 & 0.1 & 0.3 \\
\end{Bmatrix}$$



위 행렬은 학습이 모두 완료된 상태라 가정한다면 A는 가수 Feature에 대한 가중이 높고,  A와 [ B,C ]는 장르 Feature에 대한 취향이 정반대라고 추론할 수 있습니다.

아이템 행렬 I는 아래와 같을 수 있습니다.



$$\begin{align} \begin{Bmatrix}
2 & -5 & 3 \\
6 & 4 & 1 \\ 8 & 2 & 1 \\
\end{Bmatrix} \end{align}$$



아이템 A,B,C 에대한 행렬입니다. B,C는 국내, A는 국외 음반이라 가정 합니다. 여기서는 발매지역을 국내/국외로 한정하기로 합니다. 학습된 결과, 유저행렬 U의 Row 벡터의 2번째 피쳐(발매지역)가 음수이면 국외 컨텐츠에 대한 Attraction 강도가 크다고 해석할 수 있습니다(음수*음수는 양수이므로).



R 은 아래와 같다고 한다면,



$$\begin{align} \begin{Bmatrix}
8 & 4 & 6 \\
0 & 3 & 3 \\ 1 & 4 & 5 \\
\end{Bmatrix} \end{align}$$



유저 A가 아이템 A,B,C에 대해 Attraction이 발생한 횟수는 8번,4번,6번 입니다. 모델의 적합도는 R과  $$\mathbf{U}\bullet\;\; \mathbf{I}^T$$ 의 차이가 적을 수록 높다고 볼 수 있습니다. 실제로 계산을 해보면,



$$\begin{align*} \begin{Bmatrix}
0.9 & -0.8 & 0.7 \\
0.3 & 0.3 & 0.4 \\ 0.6 & 0.1 & 0.3 \\
\end{Bmatrix} \bullet \begin{Bmatrix}
2 & -5 & 3 \\
6 & 4 & 1 \\ 8 & 2 & 1 \\
\end{Bmatrix}^T \;= \; R' \end{align*}$$



이는, 

$$\begin{align*} \begin{Bmatrix}
0.9 & -0.8 & 0.7 \\
0.3 & 0.3 & 0.4 \\ 0.6 & 0.1 & 0.3 \\
\end{Bmatrix} \bullet \begin{Bmatrix}
2 & 6 & 8 \\
-5 & 4 & 2 \\ 3 & 1 & 1 \\
\end{Bmatrix} \;= \; \begin{Bmatrix}
8.1 & 3.7 & 6.1 \\
0.3 & 3.4 & 3.4 \\ 1.7 & 4.3 & 5.3 \\
\end{Bmatrix} \end{align*}$$



이고 R'과 R을 비교해 보았을때 거의 비슷하게 나오는 것을 확인할 수 있습니다. 학습이 매우 잘되었네요! 

아래부터 설명할 Spotify의 Recommendation System의 알고리즘은 유저들의 Interaction Data들을 활용해 R행렬을 만들고, 이에 적합한 U행렬과 I행렬을 찾아가는 과정으로 축약 할수 있습니다. 실제로 위와 같이 적합한 수치들을 가진 U와 I행렬을 만드는 과정을 하나하나 짚어가보도록 하겠습니다.



대략적인 설명은 위와 같고, 아래 내용부터는 논문 그대로 수식을 옮겨 설명해 보도록 합니다 ㅎㅎ



















