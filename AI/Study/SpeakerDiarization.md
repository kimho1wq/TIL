# Speaker Diarization

### 문제 정의

현재 End-to-end neural diarization(EEND) 시스템은 Tranforsmer 구조 네트워크를 사용하여
multi-label classification으로 Speaker Diarization 시스템을 직접적으로 학습시키고 있다

자연어 처리(NLP) 분야에서 효과적인 구조로 알려진 Transformer는
attention 메커니즘을 활용하여 sequence의 long-range context 정보를 학습하고
이를 바탕으로 frame들 간의 global한 정보를 추출한다

Speaker Diarization 시스템에서 이러한 Transformer 구조는
speaker 간의 관계를 나타내는 long-range 특징을 다루는데 특화되어 있지만,
speaker change와 같은 local 특징을 추출하는데 충분한 구조는 아니다
또한 speaker 를 구분할 때 주로 사용되는 phoneme과 같은 speaker-specific
information은 local spatial context 에 주로 존재한다

그렇기 때문에 화자 분할 시스템은 화자간 관계를 구분하기 위한 long-range 특징과,
speaker change나 speaker-specific information을 위한 local spatial 특징이 필요하다
따라서 global한 특징을 추출하면서 그 정보들을 cnn을 이용하여 local 특징으로 결합하는 방법을 사용해 볼 수 있다

다만 CNN은 공간적인 계층 구조와 이미지의 지역 패턴을 캡처하기 위한 local 문맥을 고려한 모델링으로,
glocal context를 융합하는데 한계가 존재한다
따라서 CNN은 long-range dependecies를 다루는데 한계가 있기 때문에 이러한 문제를 해결하기 위해
Transformer와 CNN을 결합한 Conformer를 사용한다면 long-range global context와
local spatial context를 동시에 고려한 모델링을 할 수 있다













