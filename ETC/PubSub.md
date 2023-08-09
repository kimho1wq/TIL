# Pub/Sub
- Pub/Sub 모델은 비동기 메세징 전송 방식으로, 발신자의 메세지에는 수신자가 정해져 있지 않은 상태로 publish 한다
- 그리고 이를 Subscribe(구독)을 한 수신자만 정해진 메세지(topic)을 받을 수 있다
- 이처럼 수신자는 발신자 정보가 없어도 원하는 메세지만 수신할 수 있으며, 이런 구조 덕분에 높은 확장성을 확보할 수 있다

- Pub/Sub 모델의 구체적인 발행/구독 방식은 각 서비스 마다 다른데, 대표적으로 Kafka, Redis, RabbitMQ 등이 존재

### Reids의 동작 방식 및 특징
- Redis 는 데이터베이스, 캐시, 메시지 브로커 및 스트리밍 엔진으로 사용되는 인메모리 데이터 구조 저장소이다
- 구성 요소
  - publisher: 메세지를 게시(pub)
  - channel: 메세지를 쌓아두는 queue
  - subscriber: 메세지를 구독(sub)
- 동작
  - pusblisher가 channel에 메세지 게시
  - 해당 채널을 구독하고 있는 subscriber가 메세지를 sub해서 처리함
- 특징
  - channel은 이벤트를 저장하지 않음
  - channel에 이벤트가 도착 했을 때 해당 채널의 subscriber가 존재하지 않는다면 이벤트가 사라짐
  - subscriber는 동시에 여러 channel을 구독할 수 있으며, 특정한 channel을 지정하지 않고 패턴을 설정하여 해당 패턴에 맞는 채널을 구독할 수 있다
- 장점
  - 처리 속도가 빠름
  - 캐시의 역할도 가능
  - 명시적으로 데이터 삭제 가능
- 단점
  - 메모리 기반이므로 서버가 다운되면 Redis 내의 모든 데이터가 사라짐
  - 이벤트 도착 보장을 못함


### RabbitMQ 의 동작 방식 및 특징
![image](https://github.com/kimho1wq/TIL/assets/15611500/250b8fd3-36dc-41a4-af08-bab525c02623)
- RabbitMQ는 AMQP(Advanced Message Queuing Protocol) 프로토콜을 구현한 메세지 브로커이다
- 구성 요소
  - producer: 메세지를 보냄
  - exchange: 메세지를 목적지(큐)에 맞게 전달
  - queue: 메세지를 쌓아둠
  - consumer: 메세지를 받음
- 메세지 처리 과정
  - 주체가 Producer로 Producer가 메시지를 전달할 시기를 통제하며 Broker로 메세지를 보냄(Push 형태)
  - Broker 내 Exchange(메세지 교환기) 에서 해당하는 key에 맞게 큐에 분배한다 (Binding or Routing 이라고 함)
    - topic 모드 : Routing Key가 정확히 일치하는 Queue에 메세지 전송 (Unicast)
    - direct 모드 : Routing Key 패턴이 일치하는 Queue에 메세지 전송 (Multicast)
    - headers 모드 : Key:Value로 이루어진 header값을 기준으로 일치하는 Queue에 메세지 전송 (Multicast)
    - fanout 모드 : 해당 Exchange에 등록도니 모든 Queue에 메세지 전송 (Broadcast)
  - 해당 큐에서 Consumer가 메세지를 받는다
  - 
- 장점
  - Broker 중심적인 형태로 publisher와 consumer 간의 보장되는 메세지 전달에 초점을 맞추고, 복잡한 라우팅 지원
  - 클러스터 구성이 쉽고 Manage UI 가 제공되며 플러그인도 제공되어 확장성이 뛰어남
  - 20kb/sec 정도의 속도
  - 데이터 처리 보단, 관리적 측면이다 다양한 기능 구현을 위한 서비스를 구축할 때 사용
- 단점
  - MQ Server가 종류 후 재기동되면 기본적으로 Queue 내용은 모두 제거된다 (데이터 손실의 위험성)
  - 성능 문제
  - Producer와 Consumer 간의 결합도가 높다




### Kafka의 동작 방식 및 특징
![image](https://github.com/kimho1wq/TIL/assets/15611500/264ec157-6d87-44ea-9ae9-208f0ed6548e)
- LinkedIn에서 개발된 pub-sub 모델의 메시지큐 방식 기반, 분산 메시징 시스템
- 구성 요소
  - Event(Message): kafka에서 producer 와 consumer가 데이터를 주고받는 단위 
  - Producer: kafka에 이벤트를 게시(post, pop)하는 클라이언트 어플리케이션
  - Consumer: Topic을 구독하고 이로부터 얻어낸 이벤트를 받아(Sub) 처리하는 클라이언트 어플리케이션
  - Topic: 이벤트가 모이는 곳으로 producer는 topic에 이벤트를 게시하고, consumer는 topic을 구독해 이로부터 이벤트를 가져와 처리
  - Partition: Topic은 여러 Broker에 분산되어 저장되며, 이렇게 분산된 topic을 partition이라고 함
  - Zoopeeper: 분산 메세지의 큐의 정보를 관리
- 동작 원리
  - publisher는 전달하고자 하는 메세지를 topic을 통해 카테고리화 한다
  - 주체가 subscriber로 원하는 topic을 구독(=subscribe)함으로써 메시지를 읽어온다(pull 형태)
  - publisher와 subscriber는 오로지 topic 정보만 알 뿐, 서로에 대해 알지 못한다
  - kafka는 broker들이 하나의 클러스터로 구성되어 동작하도록 설계
  - 클러스터 내, broker에 대한 분산처리는 ZooKeeper가 담당한다
- 장점
  - 대규모 트래픽 처리 및 분산 처리에 효과적
  - 클러스터 구성, Fail-over, Replication 같은 기능이 있음
  - 100Kb/sec 정도의 속도 (다른 메세지 큐 보다 빠름)
  - 디스크에 메세지를 특정 보관 주기동안 저장하여 데이터의 영속성이 보장되고 유실 위험이 적고, Consumer 장애 시 재처리가 가능하다

### Kafka vs RabbitMQ vs Redis

|        | Kafka                                                                                           | RabbitMQ                                                                                                 | Redis Pub/Sub                                                                                                                                                |
|--------|-------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 라우팅    | 기본기능으로 라우팅에 대해서 지원하지 않는다. Kafka Streams를 활용하여 동적라우팅을 구현할 수 있다.                                  | Direct, Fanout, Topic, Headers의 라우팅 옵션을 제공하여 유연한 라우팅이 가능하다.                                              | -                                                                                                                                                            |
| 프로토콜   | 단순한 메시지 헤더를 지닌TCP 기반의 custom 프로토콜을 사용하기 때문에 대체가 어렵다.                                            | AMQP, MQTT, STOMP 등 여러 메세징 플랫폼을 지원한다.                                                                    | RESP (Redis Serialization Protocol) - TCP 통신                                                                                                                 |
| 우선순위   | 변경 불가능한 시퀀스 큐로, 한 파티션 내에서는 시간 순서를 보장한다. 하지만 여러 파티션이 병렬로 처리할 때는 시간 순서 보장 못함                      | priority queue를 지원하여 우선 순위에 따라서 처리가 가능하다.                                                                | 이벤트가 도착할 지 보장도 못함 (우선순위 처리 또한 불가)                                                                                                                            |
| 이벤트 저장 | 이벤트를 삭제하지 않고 디스크에 저장함으로 영속성(durability)이 보장되고, 재처리가 가능하다.                                       | 메세지가 성공적으로 전달되었다고 판단될 경우 메세지가 큐에서 삭제되기 때문에 재처리가 어렵다                                                      | 저장하지 않고, channel에 이벤트가 도착했을 때 해당 채널의 subscriber가 존재하지 않으면 이벤트 사라짐                                                                                            |
| 장점     | - 이벤트가 전달되어도 삭제하지 않고 디스크에 저장<br/> - 고성능, 고가용성, 분산처리에 효과적<br/> - producer 중심적 (많은 양의 데이터를 병렬 처리) | - 필요에 따라 동기/비동기식 가능<br/> - producer/consumer간의 보장되는 메세지 전달<br/> - Manage UI를 제공하여 관리적 측면이나 다양한 기능 구현에 유용 | - channel을 구독하는 모든 subscriber가 이벤트를 받기 때문에 synchronization 문제에서 kafka보다 덜하다<br/> - 미들웨어가 필요 없어서 가볍다 |
| 단점     | 범용 메세징 시스템에서 제공되는 다양한 기능이 제공되지 않음                                                               | Kafka 보다 느림                                                                                              | 이벤트 도착 보장을 못함                                                                                                                                                |

- Kafka가 유리한 경우
  - 대용량 데이터 처리, 실시간, 고성능, 고가용성이 필요한 경우, 또는 저장된 이벤트를 기반으로 로그를 추적하고 재처리 하는게 필요한 경우
- RabbitMQ가 유리한 경우
  - 복잡한 라우팅을 유연하게 처리해야하고, 정확한 요청-응답이 필요한 Application을 쓸 때 또는 트래픽은 작지만 장시간 실행되고 안정적인 백그라운드 작업이 필요한 경우
- Radis가 유리한 경우
  - 이벤트 데이터를 DB에 저장하기 때문에 굳이 미들웨어에 이벤트를 저장할 필요가 없는 경우, consumer에게 굳이 꼭 알람이 도착해야한다는 보장 없이 알람처럼 push 보내는것만 중요하거나 유지보수가 편한게 중요한 경우
































