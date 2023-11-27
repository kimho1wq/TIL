# Short Utterances Speaker Verification

### 짧은 발성의 문제점

- 화자 검증(Speaker Verification) 시스템에서 짧은 발성을 사용하는 것은 잘 알려진 성능 저하의 요소이다
  - 짧은 발성을 사용한다면 audio에 speaker-specific한 정보가 충분하지 않아서 uncertainty 가 증가하게 된다

- 짧은 발성에서 speaker information를 효율적으로 사용해야 좋은 성능을 얻을 수 있다
  - multi-scale aggregation(MSA)은 다양한 time scale의 중간(intermediate) 특징들을 결합하여 다양한 길이의(짧은 발성에서도) robust한 speaker embedding을 추출하기도 한다
  - 

