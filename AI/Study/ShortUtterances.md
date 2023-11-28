# Short Utterances Speaker Verification

### 짧은 발성의 문제점

- 화자 검증(Speaker Verification) 시스템에서 짧은 발성을 사용하는 것은 잘 알려진 성능 저하의 요소이다
  - real 환경에서는 "hey siri", "okay google", "volume up" 등과  연구 환경에서 보다 같이 짧은 발성을 사용하는 경우가 많다
    -  smart speaker나 smart assistants들의 도입으로 짧은 발성에서의 화자 검증이 중요해 졌다
  - 짧은 발성을 사용한다면 audio에 speaker-specific한 정보가 충분하지 않아서 uncertainty 가 증가하게 된다
  - speaker-specific한 정보란 모델에서 각 speaker를 구분하기 위한 phonetic 정보이다  

- 짧은 발성에서 robust한 모델을 얻기 위한 가장 쉬운 방법은 짧은 발성을 이용하여 모델을 학습하는 방법이다
  - 다만 이러한 방법은 짧은 발성에 모델이 overfitting 되어 긴 발성에서의 성능을 하락 시킬 수 있다
  - 모델이 짧은 발성에 대한 uncertainty를 예측하여 speaker를 구분하기 때문에, 긴 발성에서 사용할 수 있는 phonetic 정보를 사용하지 않기 때문이다

- 짧은 발성에 대한 성능을 향상시키기 위해 TS learning이나 Segment Aggregation 방법을 사용할 수 있다
  - 이 방법은 짧은 발성으로 학습된 시스템에서 사용하지 않는 phonetic 정보를 긴 발성에서 학습된 시스템에서 가져와 학습시키는 방법이다
  - 특히, utterance-level이 아닌 phonetic-level에서 speaker-specific한 정보를 추출시키게 만든다 

- 짧은 발성에서 speaker information를 효율적으로 사용해야 좋은 성능을 얻을 수 있다
  - multi-scale aggregation(MSA)은 다양한 time scale의 중간(intermediate) 특징들을 결합하여 다양한 길이의(짧은 발성에서도) robust한 speaker embedding을 추출하기도 한다
  - deep layer aggregation(DLA)에서의 iterative deep aggregation(IDA) 방법은 다른 time scale들을 merging하여 각기 다른 temporal 정보를 포함하여 speaker embedding을 만든다
    - 또한 DLA은 hierarchical deep aggregation(HDA)를 사용하여 다양한 channel의 output을 결합하여 각기 다른 spectral 정보를 포함한 embedding을 추출하기도 한다
  - dynamic scaling policy(DSP)를 기반으로 down-sampling, up-sampling의 다양한 resolution에 대한 입력에서 정보를 획득하는 방법을 사용한다
    - 추가적으로 multi-head attention 기반 gate module을 사용하여 선택적으로 해당 정보를 aggregation 할 수 있다 

- 고정된 frame size의 hand-craft 특징 추출은 temporal resolution과 spectral resolution을 동시에 최적화 할 수 없다
  - hand-craft에서 frequency를 rescaling하여 magnitude spectrum을 추출할 때 frequency band를 감소시키고 phase를 무시하면서 추출하기 때문에, temporal resolution의 정보 손실이 있을 수 있다
    - 또한 window size가 증가할 수록 더 좋은 spectral resolution을 추출하고 그에 따라 temporal resolution은 부족해지며, size를 작게하면 반대가 된다
  - multi-resolution encoder(MRE)를 이용하여 다양한 temporal resolution과 spectral resolution을 추출하여 사용한다
    - MRE은 다양한 kernel size의 convolutional layer를 사용하여 temporal resolution를 추출한다








  