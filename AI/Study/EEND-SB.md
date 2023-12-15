# EEND-SB

### Main Contribution
(1) 
Separation mask와 Speaker embedding extractor를 활용하여 새로운 Hybrid Speaker Diarization Model을 제안하였다
separation mask를 생성하기 위해서 기존 transformer 구조의 EEND 시스템을 활용하였고
화자 분할 loss를 이용하여 who spoken when에 대한 정보를 파악하도록 학습시켰다
또한 분리된 feature map을 speaker embedding extractor에 입력하여 각 화자에 대한 speaker embedding을 출력하여
최종적으로 embedding을 활용하여 clustering 방법을 적용하였다

(2)
Auxiliary loss를 추가적으로 사용하여 모든 transformer block에서 task-relevant한 정보를 추출할 수 있도록 학습하였다
특히 lower layer에 VAD loss를 사용하여 기존 clustering-based 시스템과 유사하게 low-level layer에서는 vad 정보를 추출하도록 하였고
추가적으로 multi-level feature aggregation을 사용하여 모든 transformer block에서 task-relevant한 정보를 학습하도록 하였다


### 문제 정의

1. Hybrid 화자분할 시스템

Speaker Diarization 시스템은 recording된 audio에서 누가 언제 말했나인 who spoke when을 찾아내는 시스템이다
이러한 화자 분할 시스템은 회의록 전사나 speech-related application에서 주로 사용되는 필수적인 기술이다

전통적인 화자 분할 방법은 clustering-based 방법으로 vad, speaker embedding extractor, clustering 등의
여러가지 모듈들을 개별적으로 최적화 시키고 이를 동시에 사용하는 방법으로 진행되었다

이러한 방법은 각각의 모듈의 경량화가 잘 되어있어 속도가 빠르고 각 모듈에 맞는 데이터로 학습이 진행되므로
아직까지 좋은 성능을 가지고 있다
다만 speech가 중첩되어있는 경우를 고려하지 않은 방법이고, 각각의 모듈이 따로따로 최적화 되기 때문에
실제 화자분할 시스템에 적합하게 학습되어있다고 보장할 수 없다

그래서 최근 각각의 모듈을 하나의 큰 네트워크로 통합하는 방법으로 End-to-end neural network(EEND)가 제안되었다
이러한 EEND 방식은 실제 화자분할에서 출력되는 output과 같은 label과 데이터셋을 활용하여 binary classification으로
네트워크를 end-to-end 방식으로 학습한다

이러한 방식은 기존에 분리되어있던 모듈을 동시에 최적화하여 서로의 정보를 공유할 수 있으며,
multi-label의 활용이 가능하기 때문에 speech가 중첩이 되어있어도 학습이 가능하다는 장점이 존재한다

다만 이러한 방식은 data driven 방식이기 때문에 아직까지 일반화 성능이 부족하여 
전통적인 clustering 방식보다 성능이 떨어진다는 단점이 존재한다

그래서 최근에 활용되고 있는 시스템은 EEND 방식과 clustering 방식의 장점을 결합하여 사용하는
hybrid diarization approach 시스템이 늘어나고 있다
대표적으로 최근 state-of-the-art 시스템인 EEND-VC 모델은 speaker embedding을 출력하는 동시에
화자 분할 loss를 사용하여 네트워크를 end-to-end 방식으로 학습하고 있다

이러한 방식은 기존에 나뉘어져있던 모듈들을 하나의 네트워크에서 동시에 최적화 할 수 있을 뿐만아니라
중첩된 음성에 대해서도 처리 가능하고 inference시에는 speaker embedding을 이용한 clustering 방법을
사용하여 inter-block permutation ambiguity problem를 해결하며, 가변적인 화자 처리도 가능하고
아직까지 부족한 일반화 성능을 향상키실 수 있는 방법론이다

2. EEND-VC 시스템의 문제점

Hybrid 화자분할 시스템의 state-of-the-art인 EEND-VC는 화자분할 네트워크에서 출력된 output을 이용하여
global number의 speaker embedding을 생성하여 clustering하는 방법이다
다만 이 때 global speaker embedding을 만들때 화자분할 output을 그대로 사용하고 있다

EEND-VC에서는 네트워크의 마지막 output feature map을 각 화자의 수만큼 나누고
나눠진 feature map을 각 time별로 weighted sum하여 global speaker embedding을 생성하게 된다

이러한 방법은 1개의 frame의 정보만을 이용하여 speaker embedding을 생성하는 방법과 동일하다
하지만 이러한 embedding 생성 방법은 화자인식 분야에서 볼 수 있듯이 speaker-specific한 정보를 추출해 내기가 어렵기 때문에
결국 global speaker embedding을 이용하여 clustering할 경우 좋은 성능을 낼 수 없다

또한 화자분할 output은 3명과 같이 작은 수의 local number 수의 화자의 관계를 파악하기 위한 정보이기 때문에
이러한 local speaker embedding을 그대로 global speaker embedding으로 사용한다면 일반화 성능이 부족할 것이다

따라서 vector clustering 방법에서 분별력 있는 speaker embedding을 추출하기 위해서는 특정 화자가 발성한 부분을 추출하고
각 화자가 발성한 부분의 frame을 aggregation하여 global speaker embedding을 생성하는 것이 바람직 할 것이다

### 관련 연구
1. speech separation(eend-ss)
2. eend-vc
3. speaker embedding extractor

### 제안 기법

1. separation mask를 활용하여 global speaker embedding 추출

음성 분리(speech separation) 과정은 여러 명의 화자의 발성이 혼합된 입력 오디오에서 개별 음성을 추출하는 작업이다
음성분리 과정에서는 각 화자의 음성을 생성하기 위해서 화자의 수만큼 separation mask를 생성하고
혼합된 feature map에 separation mask를 적용하여 각각의 화자가 발성한 부분을 분리해내는 과정을 수행한다

결국 이 separation mask를 생성하는 과정이 who spoken when 을 찾아내는 일이라고 볼 수가 있는데
화자 분할에서는 전체 시스템에서 화자가 발성한 부분을 알아내기 위해 학습이 된다
따라서 화자분할 시스템을 이용하여 각 화자가 존재하는 부분의 feature map을 분리할 수 있는 separation mask를 생성할 수 있다

이렇게 생성된 separation mask를 이용하여 혼합된 feature를 개별 화자의 feature map으로 분리한 다음
각각의 feature map을 speaker embedding extractor에 넣어 global embedding을 추출한다
이렇게 추출된 global embedding을 이용하여 clustering으로 가변수 화자분할이 가능하다

추출된 speaker embedding과 embedding lookup table간의 intra-speaker embedding distance를 최소화하고
inter-speaker embedding distance를 최대화 하는 방향으로 학습하여 분별력 있는 embedding을 출력하도록 한다

또한 who spoken when 정보를 잘 추출하기 위해서 encoder의 output을 화자분할 pit loss 학습하여
개별 화자의 음성에 대한 feature map을 잘 분리해 낼 수 있도록 학습한다


2. Transformer block에 VAD loss와 multi-level feature aggregation 방법을 적용한 이유

기존 화자분할 시스템에서의 transformer-based model 학습에서는 최종적인 block의 output에 화자분할 loss로 학습을 진행하였다
다만 이러한 방식은 lower layer 부분의 block에서는 task과 관련이 없는 정보만을 추출하게 학습이 되기 때문에
화자분할과 같은 특정 classification task의 성능을 하락시키는 요인이 되기도 한다

따라서 이러한 요인을 제거하기 위해서 multi-scale feature aggregation 이나 lower layer block에
직접적으로 loss 함수를 추가하여 학습하는 방식이 제안되었고 주요한 성능 향상이 원인이 되기도 하였다
따라서 화자분할 task에 relvant한 정보를 추출하기 위해서 두가지 방법을 전부 적용하였다

기존 clustering-based 시스템에서는 초기에 speech가 존재하는지 아닌지를 판단하는 vad 모듈을 사용하는 형식이였다
따라서 EEND 네트워크의 lower layer에서 vad의 정보를 추출하기 위해 vad loss를 auxiliary loss를 사용하였다
또한 모든 transformer block에서 task-relevant한 정보를 추출하기 위해서 각각의 block의 feature을 aggregation하여 최적화 학습을 진행하는 방식을 사용하였다




