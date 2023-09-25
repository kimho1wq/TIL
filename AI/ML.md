# ML(Machine Learning)

### Machine Learning 이란
- 기계 학습이란 특정한 응용 역역에서 발생하는 데이터를 이용하여, 높은 성능으로 문제를 해결하는 컴퓨터 프로그램을 만드는 작업을 뜻한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/7a290f7b-850c-412f-8265-937f9aab57c2)
- 기계 학습 개념
  - 데이터의 군집을 이용해여 예측(prediction) 문제를 풀기위해 사용될 수 있으며, 실수값을 예측하는 회귀(regression) 문제와 부류를 예측하는 분류(classification) 문제가 있다
  - 특징 벡터(feature vector) x와 목표값(target) y의 데이터로 나타내며, 학습집합(learning set) 또는 훈련집합(training set)이라고 한다
  - 기계 학습이란 가장 정확하게 예측할 수 있는 모델(ex, $y = w_{1}x_{1} + w_{2}x_{2} + b$)에서의 최적의 매개변수(parameter)를 찾는 작업이라고도 할 수 있다
  - 새로운 샘플을 가진 데이터를 테스트집합(test set)이라 하는데, 테스트집합에 대해 높은 성능을 가진 성질을 일반화(generalization) 능력이라 한다
  - 최적의 매개변수 값을 찾기 위해서 목적 함수(objective function) 또는 비용함수를 만들고, 오차를 최소화 하는 방향으로 매개변수를 학습시킨다
    - 목적함수값(오차값)이 작아지는 방향(0을 향하여)을 찾아 매개변수 값을 조정해야 하므로, 미분을 사용한다
  - 데이터의 수 보다 모델의 용량이 작다면 과소적합(underfitting)이 발생하고, 반대라면 과잉적합(overfitting)이 발생한다
    - 훈련집합과 테스트집합의 성능에 큰 차이가 있다면 일반화(generalization) 능력이 무척 낮다고 할 수 있다
  - 편향(bias)와 분산(variance)의 관계
    - 낮은 편향과 낮은 분산을 가진 예측기(predictor)를 만들어야 한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/548523b5-62b9-4080-a3f5-c4ee496ff737)
  - 현대 기계 학습에서는 용량이 충분히 큰 모델을 선택한 후 여러 가지 규제(regularization) 기법을 적용하는 방법을 채택한다
    - 가중치 감쇠(weight decay)는 가중치를 작게 유지하게 만듬으로써 일반화 능력을 향상시키는 방법이다
  - 지도 방식에 따른 유형
    - 지도 학습(supervised learning): 데이터가 입력(특징 벡터)과 출력 쌍으로 주어지는 회귀와 분류
    - 비지도 학습(unsupervised learning): 입력(특징 벡터)만 주어지고, 특징 공간의 변환(manifold, pca 등)을 사용하여 주로 가까이 있는 샘플을 군집으로 모으는 군집화를 한다
    - 강화 학습(reinforcement learning): 연속된 샘플의 열에 목표값 하나만 주는 방식으로, 샘플 열에 속한 각각의 샘플에 목표값을 배분하는 알고리즘이 추가로 필요하다
    - 준지도 학습(semi-supervised learning): 소량의 데이터에만 부류 정보를 부여한 후, 부류 정보가 없는 대량의 데이터를 함께 활용하는 방식
  - 학습 방식에 따른 유형
    - 결정론적(deterministic), 스토캐스틱(stochastic) 학습: 대부분의 학습 알고리즘으로, 초기 난수를 동일하게 설정한다면 같은 DB로 학습했을 때 같은 예측기가 만들어 진다
    - 분별(discriminative) 학습: 샘플의 부류를 예측하는 것으로 x데이터가 y부류일 확률(조건부 확률 또는, 사후 확률(P(y|x)))을 계산한다
    - 생성 모델(generative model) 학습: x가 발생할 확률(P(x))와 부류y 에서 x가 발생할 확률(P(x|y))를 이용하여 새로운 샘플을 생성한다


### 기계 학습과 수학
- 벡터와 행렬
  - 기계 학습에서는 입력된 샘플을 특징 벡터로 표현한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/bdd5ea8a-7592-4a19-b3f1-2c7d448f4c62)
  - 벡터(vector) $x$를 4차원이라고 한다면 수학적으로 $x \in R^4$ 라고 표현한다(x는 4차원 실수 공간의 한 점)
  - 행렬(matrix) $X$는 여러 개의 벡터를 담고, 가로(row)와 세로(column) 150x4 크기의 행렬은 $x \in R^{150*4}$ 라고 표현한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/82ab4706-74f4-4c33-8a7b-1421bfd53432)
  - $d$차원 벡터는 $d*1$ 행렬로 간주할 수 있으며, 기계 학습에서 훈련집합을 담은 행렬을 설계 행렬(design matrix)라고 한다
  - $A^{T}$ 는 $A$ 행렬의 전치(transpose)라고 하며, $A$의 행과 열을 교환한 형태이다
  - 차원이 같은 두 벡터 $a$와 $b$의 곱을 내적(dot product)라고 한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/3bb3550e-77cc-4bcb-899e-f0dc0756e2bb)
  - 모든 차원을 포괄하는 표현으로 텐서(tensor)라는 용어를 사용하며, 스칼라는 0차원, 벡터는 1차원, 행렬은 2차원 텐서에 해당한다
- 놈(norm)과 유사도(similarity)
  - 벡터의 크기를 정의할 때는 놈(norm)을 사용하며, p차 norm을 Lp놈이라 한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/8972e870-2968-48c8-baa0-f6d382a7c8c8)
  - 2차 놈($||x||_{2} = (x_{1}^2 + x_{2}^2 + ... + x_{d}^2)^{1/2}$)을 유클리디언 놈(Euclidean norm) 또는 L2놈이라 하고, 가장 많이 쓰이며 종종 첨자를 생략하고 표기한다
  - 길이가 1인 벡터를 단위 벡터(unit vector)라고 부르며, L2놈으로 벡터를 나눠서 만든다 $\frac{x}{||x||_{2}}$
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/e30ce6dc-c862-4a68-915b-83e146b60296)
  - 프로베니우스 놈(Frobenius norm)은 요소들의 제곱합의 제곱근이다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/87a417ef-b8c2-43c5-a4ff-a31db77a7d7c)
  - 코사인 유사도(cosine similarity): 벡터를 단위 벡터로 만들어 내적을 이용하여 유사도를 측정하는 방법
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/4176e37c-72c5-48fc-b0ff-dca88f417838)
- 퍼셉트론(perceptron)
  - 입력 샘플을 2개의 부루 중 하나로 분류하는 분류기(classifier)이다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/a0db3b83-6c99-42e9-abd6-236b9d467745)
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/b58a1988-8aa8-490e-8c1e-a82381ade70a)
    - d차원의 특징 벡터 x를 입력하여 해당 wedge의 가중치를 곱하여 더한값을 활성값(activation value)라고 한다
    - 활성값을 활성함수(activation function)에 넣었을 때 출력되는 값이 output이 된다
    - 전체 공간을 분할하는 결정 직선(decision line)은 가중치(w)에 수직이고, 원점으로부터 $\frac{T}{||w||_{2}}$
  - 가중치 벡터($w$)를 각 부류의 기준 샘플이라고 생각한다면, 신경망은 입력된 특징 벡터 $x$에 대해 $c$개의 부류와 유사도(벡터의 내적)를 계산하는 것이다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0ca1774b-b708-49b7-8c1d-5170e089fb59)
- 선형결합과 벡터공간
  - 벡터 $a$와 $b$에 각 상수를 곱한 후 더하는 연산($c = \alpha_{1}a + \alpha_{2}b$)을 선형결합(linear combination)이라고 한다 
  - 선형결합으로 만들어지는 공간을 벡터공간(vector space)라고 하고, 벡터공간을 만드는 벡터들을 기저 벡터(basis vector)라고 한다
  - 서로 수직인 기저 벡터를 정규 직교(orthonormal) 기저 벡터라고 하며, 한 기저 벡터를 나머지 기저 벡터의 선형결합으로 만들 수 없다면 선형독립(linear independence)하다고 한다
  - 행렬의 계수(rank)란 선형독립인 행의 갯수로 나타내고 계수가 n이면 벡터공간이 n차원이 되며, 행렬의 계수가 벡터의 차원과 같으면 행렬이 최대계수(full rank)를 가졌다고 말한다
- 역행렬
  - 정사각행렬 A에 대해 $A^{-1}A = AA^{-1} = I$를 만족하는 행렬 $A^{-1}$를 $A$의 역행렬(inverse matrix)라고 한다
  - 역행렬이 없는 행렬을 특이행렬(singular matrix)라고 하며, A가 역행렬을 가질때 다음 조건은 전부 필요충분조건이다
    - ```
      - A는 특이행렬이 아니다.
      - A는 최대계수를 가진다.
      - A의 모든 행이 선형독립이다.
      - A의 모든 열이 선형독립이다.
      - A의 행렬식은 0이 아니다.
      - A^(T)A는 양의 정부호(positive definite) 대칭 행렬이다.
      - A의 고윳값(eigen value)은 모두 0이 아니다.
      ```
  - A의 행렬식(determinant)은 $det(A)$로 표기하며, 크기가 4x4 이상인 행렬의 행렬식을 구하려면 가우스-요르단 소거법(Gauss-Hordan elimination)을 사용해야 한다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/825448c6-ab6a-4f7f-8bd9-282203ae857c)
    - 행렬식은 기하학적으로 2차원에서는 2개의 행 벡터가 이루는 평행사변형의 넓이이고, 3차원에서는 3개의 행 벡터가 이루는 평행사각기둥의 부피이다
  - 행렬식을 이용하여 역행렬을 구할 수 있다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/29d34948-246c-4686-9fff-c5dbf1b43c9d)
  - 정부호 행렬
    - 양의 정부호 행렬(positive definite) 행렬: 0이 아닌 모든 벡터 x에 대하여, $x^{T}Ax>0$
      - 양의 정부호 행렬의 고윳값은 모두 양수(>0)이며 모든 벡터를 양수로 만든다
    - 양의 준정부호(positive semi-definite) 행렬: 0이 아닌 모든 벡터 x에 대하여, $x^{T}Ax>=0$
    - 음의 정부호(negative definite) 행렬: 0이 아닌 모든 벡터 x에 대하여, $x^{T}Ax<0$
    - 음의 준정부호(negative semi-definite) 행렬: 0이 아닌 모든 벡터 x에 대하여, $x^{T}Ax<=0$
  - 부정부호 행렬(indefinite matrix): 벡터에 따라 음수가 되기도 하고 양수가 되기도 한다
- 고유 벡터(eigen vector)와 고유값(eigen value)
  - $Av = \lambda v$를 만족하는 0이 아닌 벡터 $v$를 행렬 $A$의 고유 벡터(eigen vector)라고 하며, 실수 $\lambda$를 고윳값(eigen value)라고 한다
  - m*m 행렬은 최대 m개의 고윳값과 고유 벡터를 가질 수 있으며, 모든 고유 벡터를 서로 직교(orthogonal) 한다(벡터의 내적 = 0)
  - 고유값 분해(eigen value decomposition)
    - 행렬 $A$를 $A = QA^{'}Q^{-1}$ 로 분해할 수 있다
    - $Q$는 $A$의 고유 벡터(eigen vector)를 열에 배치한 행렬이고, $A^{'}$는 고유값(eigen value)를 대각선에 배치한 대각행렬이다
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/8e0d7111-83e1-4b4b-adfb-a8dd38095b7b)
  - A가 정사각행렬에서 $det(A-\lambda I)=0$을 만족하는 방정식을 풀어서 고유값(eigen value)를 구할 수 있다 
- 확률변수와 확률분포
  - 시행의 결과에 따라 값이 결정되는 변수를 확률변수(random variable)라고 하고, 확률변수가 가질 수 있는 값의 집합을 정의역이라고 한다
  - 정의역 $x \in 1,2,...$ 전체에 결체 확률을 표현한 것을 확률분포(probability distribution)라고 하며 $P(x=1)$로 표현할 수 있다
  - 정의역이 이산일 때의 확률분포를 확률질향함수(probability mass function)이라 하고, 연속일 때는 확률밀도함수(probability density function)라고 한다
- 곱 규칙과 합 규칙
  - 결합 확률(joint probability): 두 사건이 결합된 상태의 확률, $P(x=하양, y=1)$
  - 조건부 확률(conditional probability): 사건이 이미 발생한 조건에서 다른 사건이 발생할 확률, $P(x=하양|y=1)$
  - 곱 규칙(production rule): $P(y,z) = P(x|y)P(y)$
  - 합 규칙(sum rule): $\displaystyle\sum_{y}^{}{P(y,z)}$ = $\displaystyle\sum_{y}^{}{P(x|y)P(y)}$
  - 독립(independent): $P(x,y) = P(x)P(y)$를 만족한다면 두 확률변수가 독립이라고 할 수 있다
  - 조건부 독립(conditional independent): 3개의 확률변수가 있을 때, $P(x,y|z) = P(x|z)P(y|z)$ 이면 x와 y는 z가 주어졌을 때 조건부 독립이라고 한다
- 기계 학습에 확률 적용
  - 사후확률(posterior probability): $P(y|x)$, 사건이 벌어져서 x를 관찰한 후 어느 y에서 발생했는지 나타내는 확률
  - 사전확률(prior probability): $P(y)$, 사건 x와 무관하게 미리 알 수 있는 확률
  - 우도(likelibood): $P(x|y)$, x는 알고 있는 사건이고 y는 추정해야 하는 사건으로 역 확률 문제( $L(y|x)$ )라고도 함
  - 베이즈 정리(Bayes formula): x,y의 결합확률이나 y,x가 일어날 결합확률이 같다
    - $P(y,x)=P(x|y)P(y) = P(x,y)=P(y|x)P(x)$
    - $P(y|x) = \frac{P(x|y)P(y)}{P(x)}$
  - ```문제) 새로운 샘플 x가 입력되었을 때, x를 y의 부류 중 하나로 분류해야 한다```
    - x가 어떤 부류에 속할 확률인 사후확률( $P(1|x), P(2|x), P(3|x)$ )을 구한 다음 가장 큰 값을 가지는 부류( $\hat{y} = argmax_{y}P(y|x)$ )로 분류해야 한다
    - 단, x가 만드는 공간은 굉장히 큰 차원으로 모든 확률을 표현할 수 없기 때문에, 베이즈 공식의 사전확률( $P(y)$ ), 우도( $P(x|y)$ )를 이용하여 계산한다
    - 무작위로 n개의 x를 추출한 후, 그 중에서 y의 비율로 사전확률( $P(y)$ )을 구할 수 있다
    - 우도( $P(x|y)$ )는 부류 y가 고정되어 있어서 다른 부류의 샘플을 배제한 후, y에 속하는 샘플만 가지고 확률 분포를 측정할 수 있다
- 평균과 분산
  - 평균(mean): $\mu = \frac{1}{n}\displaystyle\sum_{i=1}^{n}{x_{i}}$
  - 분산(variance): $V = \frac{1}{n}\displaystyle\sum_{i=1}^{n}{(x_{i}-\mu)^2}$
  - 표준편차(standard deviation): $V = \sigma^2 $
  - 특징이 d개인 데이터의 확률 벡터를 $x = (x_{1},\cdots,x_{d})^{T}$라고 한다면
    - 평균 벡터: $\mu = (\mu_{1},\cdots,\mu_{d})^{T} = \frac{1}{n}\displaystyle\sum_{i=1}^{n}{x_{i}}$
    - 공분산 행렬(covariance matrix): $\sum = \frac{1}{n}\displaystyle\sum_{i=1}^{n}{(x_{i}-\mu)(x_{i}-\mu)^{T}}$
      - $(x_{i}-\mu)$ 는 d\*1 행렬 이므로 $(x_{i}-\mu)(x_{i}-\mu)^{T}$ 는 d\*d 행렬이 된다
      - 공분산 행렬의 요소 $\sigma_{ij}$는 i번째 특징과 j번째 특징의 상호 변화 양상을 표현한다
      - 양수라면 i가 커질 때 j도 커지고, 음수라면 i가 커질 때 j는 작아진다
- 유용한 확률분포
  - 가우시안 분포(Gaussian distribution): 













