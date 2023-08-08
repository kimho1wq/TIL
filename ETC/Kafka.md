# Kafka

### Kafka의 기본 개념
- 분산 이벤트/메시지 스트리밍 플랫폼(Distributed Event/Message Streaming Platform)으로 파이프라인, 스트리밍 분석, 데이터 통합 및 미션 크리티컬 애플리케이션을 위해 설계되었다
- 고가용성(high availability, HA), 빠른처리가 가능하지만 전달 순서의 보장이 어렵고 아주 작게 사용이 어렵다
- pub/sub 구조로 Producer, Consumer의 n:m 구조로 동작하며 전달하는 쪽은 전달받는 곳에 관여할 필요가 없다
- kafka는 데이터를 이동하거나 trigger를 주는 부분에서 사용된다
  - Page Tracking: 사용자가 page 어디에 접근하였는지
  - 내/외부 api keyword 정보, 서버 로그정보, 결제 정보 등