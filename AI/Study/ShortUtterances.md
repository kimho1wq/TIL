# Short Utterances Speaker Verification

### introduction

real 환경에서의 화자 검증(Speaker Verification) 시스템은 "hey siri", "okay google", "volume up" 등과 같이 연구 환경에서 보다 같이 짧은 발성을 사용하는 경우가 많은데, 화자 검증 시스템에서 이러한 짧은 발성을 사용하는 것은 잘 알려진 성능 저하의 요소이다

화자 검증 시스템에서 짧은 발성을 사용한다면 audio에 speaker-specific한 정보가 충분하지 않아서 uncertainty 가 증가하게 된다
speaker-specific한 정보란 모델에서 각 speaker를 구분하기 위한 정보로,
화자 인증 시스템에서는 화자 정보를 특정하기 위해 audio에서 특정 음소(phoneme) 정보를 바탕으로 학습하게 된다
음소(phoneme) 정보는 text independent의 화자 인식에서 불필요한 존재이지만 대부분의 시스템에서 phoneme 정보를 사용하여 성능을 향상시킨 결과가 존재한다
이러한 이유는 화자 인식 시스템의 segment-based pooling mechanism에서 speaker-specific한 정보가 많이 포함되어 있는 특정 phoneme 정보에 집중하기 때문이다
때문에 발성 audio가 짧아 진다면, 해당 speaker의 phoneme 정보가 부족해져 모델의 일반화성능이 떨어지게 된다

이러한 짧은 발성을 사용하는 화자 인증 시스템에서 robust한 모델을 얻기 위한 가장 쉬운 방법은
해당 시스템을 학습할 때 짧은 발성을 직접적으로 이용하여 모델을 학습하는 방법이 있다
다만 이러한 방법은 모델이 짧은 발성에 대한 uncertainty를 예측하여 speaker를 구분하기 때문에,
긴 발성에서 사용할 수 있는 phonetic 정보를 사용하지 않고, 해당 모델이 짧은 발성에 모델이 overfitting 되어
전체적으로 긴 발성에서의 성능을 하락 시킬 수 있다


짧은 발성에 대한 성능을 향상시키기 위해 TS learning이나 Segment Aggregation 방법과 같은 2phase 방법을 사용할 수 있다
이 방법은 짧은 발성으로 학습된 시스템에서 사용하지 않는 phonetic 정보를 긴 발성에서 학습된 시스템에서 가져와서
짧은 발성으로 테스트 하였을 때도 긴 발성에서 중점적으로 보았던 정보를 보거나 생성하게 만드는 방법이다
 
화자 검증 모델에서 audio의 길이의 성능을 최소화 하기 위해 speaker information를 효율적 사용하는 방법론이 있따
multi-scale aggregation(MSA)은 다양한 time scale의 중간(intermediate) 특징들을 결합하여
다양한 길이(짧은 발성에서도)의 robust한 speaker embedding을 추출하기도 한다

또한 deep layer aggregation(DLA)에서의 iterative deep aggregation(IDA) 방법은 다른 time scale들을 merging하여
각기 다른 temporal 정보를 포함하여 speaker embedding을 만든다
또한 DLA은 hierarchical deep aggregation(HDA)를 사용하여 다양한 channel의 output을 결합하여
각기 다른 spectral 정보를 포함한 embedding을 추출하기도 한다

rawnext와 같은 모델에서는 dynamic scaling policy(DSP)를 기반으로 down-sampling, up-sampling의 다양한 resolution에 대한 입력에서 정보를 획득하는 방법을 사용한다
추가적으로 multi-head attention 기반 gate module을 사용하여 선택적으로 해당 정보를 aggregation 할 수 있다 

고정된 frame size의 hand-craft 특징 추출은 temporal resolution과 spectral resolution을 동시에 최적화 할 수 없다
hand-craft에서 frequency를 rescaling하여 magnitude spectrum을 추출할 때 frequency band를 감소시키고
phase를 무시하면서 추출하기 때문에, temporal resolution의 정보 손실이 있을 수 있다
또한 window size가 증가할 수록 더 좋은 spectral resolution을 추출하고 그에 따라 temporal resolution은 부족해지며, size를 작게하면 반대가 된다
multi-resolution encoder(MRE)를 이용하여 다양한 temporal resolution과 spectral resolution을 추출하여 사용한다
MRE은 다양한 kernel size의 convolutional layer를 사용하여 temporal resolution를 추출한다


### proposed

|Model|Input feature|full [EER(%)/MinDCF]|5s|2s|1s|
|---|---|---|---|---|---|
|MSEA-FPM|MFB-64|1.98 / 0.205|2.17|3.38|5.92|
|ResNet34-TAP|MFB-40|2.08 / 0.234|2.20|3.00|4.77|
|ResNet34-ANF|MFB-40|1.91 / 0.221|2.04|2.88|4.49|
|ECAPA-TDNN(implementation)|MFB-80|0.98 / 0.668|1.02|1.85|4.12|
|RawNet2|Waveform|2.43|2.64|3.88|7.24|
|RawNeXt|Waveform|1.29 / 0.142|1.45|2.34|4.37|
|FDN-W-Res2MP-Net|Waveform|1.42 / 0.0925| - | - | - |
|RawNet3(implementation)|Waveform|1.15 / 0.07541|1.23|2.28|5.04|
|MRS-RawNet(proposed)|Waveform|0.87 / 0.0551|0.98|1.75|3.97|







         




  