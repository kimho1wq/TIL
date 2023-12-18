# Mel-Frequency Cepstral Coefficients(MFCCs)

![image](https://github.com/kimho1wq/TIL/assets/15611500/a7f7227e-d5c3-489f-8b20-19d7624fd319)

입력 음성을 짧은 구간으로 나누는데 이를 프레임(frame)이라고 한다

각각의 프레임에 푸리에 변환(Fourier Transform)을 실시해 해당 구간에 담긴 주파수(frequency)정보를 추출한다

모든 프레임에 fourier transform을 실시한 결과를 스펙트럼(spectrum)이라고 한다

spectrum에 사람의 말소리 인식에 민감한 주파수 영역대를 세밀하게 보고,
나머지 영역대는 상대적으로 덜 분석하는 필터인 mel filter bank를 적용하는데

이 결과를 멜 스펙트럼(mel spectrum)이라고 하고,
만약 결과에 로그를 취했다면 로그 멜 스펙트럼(log-mel spectrum)이라고 한다

MFCC는 log-mel spectrum에 역 푸리에 변환(Inverse Fourier Transform)을 적용해
주파수 도메인의 정보를 새로운 시간(time) 도메인으로 바꾼 것을 의미한다

### Preemphasis
사람의 말소리를 spectrum으로 변환해서 관찰하면 주로 모음(vowel) 부분에서
저주파(low frequency) 성분 에너지(energy)가 고주파(high frequency) 보다 많은 경향이 있다

이러한 경향 때문에 고주파 성분의 에너지에 대하여 모델이 영향을 덜 받기 때문에
고주파 성분의 에너지를 올려주면 모델의 성능을 개선할 수 있다고 하고, 이러한 방법을 pre-emphasis 라고 한다

- $y_t = x_t - \alpha x_{t-1}$, t번째 시점의 원시 음성 신호: $x_t$, $coefficient \alpha$는 보통 0.97, 0.95를 사용
1. 상대적으로 에너지가 적은 고주파 성분을 강화함으로써 원시 음성 신호가 전체 주파수 영역대에서 비교적 고른 에너지 분포를 갖도록 함
2. 푸리에 변환시 발생할 수 있는 numerical problem 예방
3. Signal-to-Noise Ratio(SNR) 개선

### Framing
음성 신호를 짧은 시간 단위로 쪼개는 과정을 의미하며
일정 시간 단위(frame_size or window_size)와, 일정 구간(frame_stride or hop_size)을 겹치게 처리한다

### Windowing
![image](https://github.com/kimho1wq/TIL/assets/15611500/b24fe45d-83d6-4273-aeee-2495e052a761)

각각의 프레임에 특정 함수를 적용해 경계를 스무딩하는 기법으로 해밍 윈도우(Hamming Window)등의 기법이 있다

원시 음성 신호를 짧은 구간 단위로 잘게 쪼개는 framing을 수행하는데 이는 Rectangular Window라,
자른 프레임 양끝에서 신호가 존재하다가 갑자기 죽는(0이 되는)상황이 발생된다

이 프레임을 그대로 푸리에 변환 하게 되면 불필요한 고주파(high frequency) 성분이 나오기 때문에
Hamming Window등의 윈도우 기법을 적용하여 양 끝부분을 스무딩해주는 것이다

### Fourier Transform
fourier transform이란 시간(time) 도메인의 음성 신호를 주파수(frequency) 도메인으로 바꾸는 과정을 의미한다
특히 이산 푸리에 변환(Discrete FT, DFT)란 이산 신호에 대한 푸리에 변환을 의미하고,
실제 적용할때는 고속 푸리에 변환(Fast FT, FFT)라는 기법을 사용하여 중복된 계산량을 줄여서 사용한다
FFT는 주파수 도메인으로 변환할 때 몇 개의 구간(bin or fft_size)으로 분석할지 정한다

### Magnitude
DFT의 결과는 복소수(complex number)로 실수부(real part)와 허수부(imeginary part)로 나뉜다

이산 푸리에 변환 결과를 $X[k] = a + b * i$ 라고 할때 주파수 성분의
진폭(magnitude)은 주파수 성분의 크기를,
위상(phase)은 해당 주파수의 위치를 나타낸다고 해석할 수 있다
![image](https://github.com/kimho1wq/TIL/assets/15611500/8921a667-0772-42ff-b1c7-98d75893db96)

결국 MFCC를 구할 때는 phase 정보는 없애고 magnitude 정보만을 남겨서 사용한다

### Power Spectrum
$k$번째 주파수 구간(bin)에 해당하는 DFT 결과를 $X[k]$라고 할 때 파워(Power)를 구하는 공식은 $power = \frac{|X[k]|^2}{fft_n}$ 이다
즉, 파워 스펙트럼(power spectrum)은 진폭(magnitude)의 제곱을 fft_size로 나눠준 값이다


### Filter Banks
사람의 소리 인식은 1000Hz 이하의 저주파수(low frequency) 영역대가 고주파수(high frequency) 대비 민감하다
그렇기 때문에 저주파수 영역대의 에너지(energe) 정보를 상대적으로 세밀하게 볼 필요가 있다

이때 적용하는 기법을 필터 뱅크(Filter Banks)라고 하고 filter banks는 멜 스케일(Mel Scale) 필터를 사용한다
기존 주파수(f, 단위:Hz)를 멜(m, 단위:mel)로 변환하는 수식은 $m=2595 log_{10}(1 + \frac{f}{700})$를 사용한다

mel 필터의 개수(n_filter=40) 정하면 fbank[0]은 저주파수 영역대를 세밀하게 살피는 필터이고, fbank[39]는 고주파수 영역대를 넓게 보는 필터이다
![image](https://github.com/kimho1wq/TIL/assets/15611500/a152341a-7501-4d02-8376-87ba5b3beb71)

### Log-Mel Spectrum
사람이 2배 큰 소리를 인식하기 위해선 실제로 100배 큰 소리가 있어야 하는 것 처럼, 사람의 소리 인식은 로그(log) 스케일에 가깝다
따라서 보통 filter-banks 기법을 적용한 멜 스펙트럼(mel spectrum)에 로그를 취하여 로그 멜 스펙트럼(log-mel spectrum)을 만들어 사용한다

### MFCCs
log-mel spectrum은 특징(feature)은 주변 몇개의 주파수 영역대의 에너지를 한데 모아 보기때문에 변수 간 상관관계(correlation)이 존재한다
이것은 변수 간 독립(independence)를 가정한 Gaussian Mixture Model(GMM)에 입력하기 어렵기 때문에,
log-mel spectrum에 역푸리에 변환(Inverse Fourier Transform, IFT)을 수행하여 변수 간 상관관계를 해소시키는 것이 MFCCs 이다

MFCCs는 상관관계가 높았던 주파수 도메인 정보가 IFT로 인해 새로운 도메인으로 바뀌어 상대적으로 변수 간 상관관계가 해소된다
MFCCs를 만들 때는 역이산 코시안 변환(Inverse Discrete Cosine Transform)을 사용하여 푸리에 변환에서 실수 파트만을 진행한다

MFCCs의 shape은 필터의 개수(n_filter)만큼 나오는데, 이 중에서 2~13번째 특징만 뽑아 최종적으로 사용한다(feat_dim = 12)


