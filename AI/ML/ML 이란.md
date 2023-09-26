# ML(Machine Learning) 이란

### 기계 학습의 개념
- 기계 학습이란 특정한 응용 역역에서 발생하는 데이터를 이용하여, 높은 성능으로 문제를 해결하는 컴퓨터 프로그램을 만드는 작업을 뜻한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/7a290f7b-850c-412f-8265-937f9aab57c2)
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







