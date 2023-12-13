# Speaker Diarization

### Main Contribution
(1) Hybrid Speaker Diarization System에 최초로 conformer-based model을 적용하였다
기존 EEND에서는 conformer-based model를 탐구한 실험이 존재하지만 simulated data 환경에서
기존 transformer-based model 모델 보다 좋은 일반화 성능을 달성하지 못하였다
하지만 global speaker embedding을 사용하는 hybrid system에서는 conformer-based 모델이
speaker-specific한 특징을 추출하기 적합하다고 판단하였고, 기존 transformer 모델보다 좋은 일반화 성능을 달성하였다

(2) Auxiliary loss를 추가적으로 사용하여 모든 conformer block에서 task-relevant한 정보를 추출할 수 있도록 학습하였다
특히 VAD loss를 lower layer에 사용하여 기존 clustering-based 시스템과 유사하게 vad된 feature를 추출하도록 하였고
multi-level feature aggregation을 사용하여 모든 conformer block에서 task-relevant한 정보를 학습하도록 하였다

(3) inference에서 사용하는 global speaker embedding의 화자분할 분별력을 높이기 위해서
각 layer에서 출력된 speaker embedding들을 concat하여 화자분할 loss를 이용하여 직접적으로 학습시켰다


### 문제 정의

1. 화자분할의 정의와 최근 화자분할 시스템의 구성 방법

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


2. EEND 시스템에서 사용하고 있는 Transformer 구조의 한계점과 Conformer를 사용하는 이유

최근 End-to-end neural diarization(EEND) 시스템은 대부분 Tranforsmer 구조 네트워크를 사용하여
multi-label classification으로 Speaker Diarization 시스템을 학습시키고 있다

자연어 처리(NLP) 분야에서 효과적인 구조로 알려진 Transformer는 attention 메커니즘을 활용하여
입력 sequence의 long-range context 정보를 학습하고 이를 바탕으로 frame들 간의 global한 정보를 추출하여 학습한다

화자분할 시스템에서 이러한 Transformer 구조는 speaker 간의 관계를 나타내는
long-range 특징을 다루는데 특화되어 있지만, speaker change와 같은 local 특징을 추출하는데 충분한 구조는 아니다
또한 EEND-VC와 같이 global speaker embedding을 추출하기 위해서는 각각의 화자를 특징할 수 있는 
speaker-specific한 정보를 추출해야 되는데 이는 보통 인접한 frame간의 관계속에 존재한다

따라서 화자분할 시스템은 local spatial feature를 추출하여 speaker change나 speaker-specific 정보를
고려해야 하며 이는 보통 convolutional neural network를 이용하여 추출할 수 있다
다만 CNN만을 사용한다면 보통 local context을 고려한 모델링으로 global context를 융합하는데는 한계가 존재한다

그렇기 때문에 화자분할 시스템은 화자간 관계를 구분하기 위한 long-range 특징과,
speaker change나 speaker-specific information을 위한 local spatial 특징이 필요하다

따라서 Transformer와 CNN을 결합한 Conformer를 사용한다면 long-range global context와
local context를 동시에 고려한 모델링을 좀더 화자분할에 적합한 모델링을 수행할 수 있을 것이다


3. Conformer block에 VAD loss와 multi-level feature aggregation 방법을 적용한 이유

기존 화자분할 시스템에서의 transformer-based model 학습에서는 최종적인 block의 output에만
화자분할 loss를 적용하여 학습을 진행하였다

다만 이러한 방식은 lower layer 부분의 block에서는 task과 관련이 없는 정보만을 추출하게 학습이 되기 때문에
화자분할과 같은 특정 classification task의 성능을 하락시키는 요인이 되기도 한다

따라서 이러한 요인을 제거하기 위해서 multi-scale feature aggregation 이나 lower layer block에
직접적으로 loss 함수를 추가하여 학습하는 방식이 제안되었고 주요한 성능 향상이 원인이 되기도 하였다
따라서 conformer based 화자분할 시스템에서도 task-relvant한 정보를 추출하기 위해서 두가지 방법을 전부 적용하였다

기존 clustering-based 시스템에서는 초기에 speech가 존재하는지 아닌지를 판단하는 vad 모듈을 사용하는 형식이였다
따라서 EEND 네트워크에서도 lower layer에서 vad를 판단하는 loss가 필요하다고 판단하여 auxiliary loss를 사용하였다

또한 모든 conformer block에서 task-relevant한 정보를 추출하기 위해서 각각의 block의 feature을
aggregation하여 최적화 학습을 진행하는 방식을 사용하였다


4. global speaker embedding을 화자 분할 loss로 직접적으로 학습시킨 이유










