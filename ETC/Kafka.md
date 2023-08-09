# Kafka

### Kafka의 기본 개념
- 분산 이벤트/메시지 스트리밍 플랫폼(Distributed Event/Message Streaming Platform)으로 파이프라인, 스트리밍 분석, 데이터 통합 및 미션 크리티컬 애플리케이션을 위해 설계되었다
- pub/sub 모델의 메시지 큐(Message Queue, MQ) 형태로 동작한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/a127d79d-0064-4d56-8485-391c81ded18f)
  - Queue(channel)라는 임시 저장소를 통해 비동기 처리가 가능하다(나중에 처리 가능)
  - Producer, Consumer의 n:m 구조로 서비스 확장이 가능하며, 전달하는 쪽은 전달받는 곳에 관여할 필요가 없다
  - MQ에 메시지가 들어간다면 consumer가 다운되더라도 메시지 전달이 보장된다
- 모든 이벤트/데이터의 흐름을 중앙에서 관리되며 kafka가 제공하는 표준 포맷으로 연결된다
- kafka는 데이터를 이동하거나 trigger를 주는 부분에서 사용된다
  - Page Tracking: 사용자가 page 어디에 접근하였는지
  - 내/외부 api keyword 정보, 서버 로그정보, 결제 정보 등
- 고가용성(high availability, HA), 빠른처리가 가능하지만 전달 순서의 보장이 어렵고 아주 작게 사용이 어렵다


### Kafka의 구성 요소
![image](https://github.com/kimho1wq/TIL/assets/15611500/6d7a06c3-940b-4809-a6dc-078e6439c4d6)
- Topic
  - 각각의 메시지를 목적에 맞게 구분할 때 사용하며 한 개의 Topic(폴더)은 한 개 이상의 Partition(하위폴더)으로 구성된다
  - 메시지를 전송하거나 소비할 때 Topic을 반드시 입력하며 Consumer는 자신이 담당하는 Topic의 메시지를 처리한다
- Partition
  - 분산 처리를 위해 사용되며 Topic 생성 시 Partition 개수를 지정할 수 있다 (파티션 개수 변경 가능. *추가만 가능)
    - Topic에 메세지가 쓰여지는 것도 어느정도 시간이 소비되어 몇 천건의 메세지가 동시에 카프카에 write 되면 병목현상이 발생할 수 있다
    - 따라서 Partition을 여러개 두어서 분산 저장함으로써 write 동작을 병렬로 처리할 수 있다
    - 다만, 한번 늘린 파티션은 운영 중에 절대 줄일 수 없기 때문에, 파티션을 늘려야 하는건 충분히 검토 후 실행되어야 한다
  - Leader/Follower가 존재하며 실제 read/write는 리더에서 발생한다. follower는 pull방식으로 leader와 sync를 맞춰 복사한다
  - 파티션이 1개라면 모든 메시지에 대해 순서가 보장되며 파티션 내부에서 각 메시지는 고유 번호(Offset)로 구분된다
  - 파티션이 여러개라면 Kafka 클러스터가 RR(Round Robin)방식으로 분배해서 분산처리되기 때문에 순서 보장이 안된다
  - 파티션이 많을 수록 처리량이 좋지만 장애 복구 시간이 늘어난다
- Offset
  - Consumer에서 메세지를 어디까지 읽었는지 저장하는 값으로 장애 발생 후 복구에 사용된다
  - Consumer그룹의 Consumer들은 각각의 Partition에 자신이 가져간 메시지의 위치 정보(offset)를 기록
- Producer
  - 메시지를 만들어서 kafka Cluster에 전송한다
  - 메시지 전송 시 Batch 처리가 가능하며 key값을 지정하여 특정 파티션으로만 전송이 가능하다
  - 전송 acks값을 설정하여 효율성을 높일 수 있다
    - ACKS = 0 : 매우 빠르게 전송한다. 파티션 리더가 받았는 지 알 수 없다
    - ACKS = 1 : 파티션 리더가 받았는지 확인한다. 기본값(default)
    - ACKS = ALL : 파티션 리더 뿐만 아니라 팔로워까지 메시지를 받았는 지 확인한다
- Consumer
  - kafka Cluster에서 메시지를 읽어서 처리한다
  - 메세지를 Batch 처리할 수 있으며 한 개의 Consumer는 여러 개의 Topic을 처리할 수 있다
  - 메시지를 소비하여도 메시지를 삭제하지는 않으며(Kafka delete policy에 의해 삭제) 한 번 저장된 메시지를 여러번 소비도 가능하다
  - Consumer는 Consumer그룹에 속하며, 한 개 Partition은 같은 Consumer그룹의 여러 개의 Consumer에서 연결할 수 없다
    - Consumer의 묶음을 Consumer그룹 이라고 하며, Consumer그룹은 하나의 topic에 대한 책임을 갖고 있다
    - 어떤 Consumer가 down된다면, 파티션 재조정(리밸런싱)을 통해 다른 Consumer가 해당 파티션의 sub을 맡아서 한다
    - offset 정보를 그룹간에 공유하고 있기 때문에 down 되기 전 마지막으로 읽었던 메세지 위치부터 시작한다
- Broker
  - Broker는 실행된 Kafka 서버 자체를 의미하며 실제 Data를 받아 저장한다, Producer와 Consumer는 별도의 애플리케이션 구성된다
  - Broker(각 서버)는 Kafka Cluster 내부에 존재하며 서버 내부에 메시지를 저장하고 관리하는 역할을 수행한다
  - Broker의 리더를 Controller라 하고, Controller는 Topic, Partition을 생성하고 복제본을 관리하는 역할을 수행한다 
- Zookeeper
  - 분산 애플리케이션 관리를 위한 코디네이션 시스템으로 분산 메시지큐의 메타 정보를 중앙에서 관리하는 역할을 수행한다
- CMAK (Cluster Manager for Apache Kafka)
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/b56d403f-d5e7-4472-99d6-aa688128fba3)
  - 웹 환경에서 Kafka Manager로 GUI 기반 관리 도구
  - cluster monitoring, topic 생성 및 변경, partition 추가 및 조정, comsumer 조회 등의 기능 수행
- Kafka cluster
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6e82d51f-23e8-40ce-a14c-db4fe4476ab4)


### Kafka 설치 및 실행 in Ubuntu
- kafka 설치
  - ```$ sudo apt-get update```
  - ```$ sudo apt-get install -y default-jre```
  - ```$ java --version```
  - ```$ mkdir kafka```
  - ```$ curl "https://archive.apache.org/dist/kafka/2.7.2/kafka_2.12-2.7.2.tgz" -o kafka/kafka.tgz```
  - ```$ cd kafka```
  - ```$ tar -xvzf kafka/kafka.tgz --strip 1```
- kafka 할당 메모리 변경 및 환경 변수 설정
  - ```$ vi bin/kafka-server-start.sh```
    - ```
      if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
          export KAFKA_HEAP_OPTS="-Xmx400m -Xms400m"
      fi
      ```
  - ```$ vi ~/.bashrc```
    - ```
      > export KAFKA_HEAP_OPTS="-Xmx400m -Xms400m"
      > export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
      > export PATH=$PATH:$JAVA_HOME/bin
      ```
- kafka config 설정
  - ```$ vi kafka/config/server.properties```
    - ```
      > advertised.listeners=PLAINTEXT://54.180.89.167:9092
      > log.dirs=/home/ubuntu/kafka/logs
      > broker.id=0 #(각 서버별로 다르게)
      > zookeeper.connect= 172.31.45.58:2181, 172.31.9.182:2181,... #(각 서버의 privateIP)
      ```
- zookeeper config 설정
  - ```$ vi kafka/config/zookeeper.properties```
    - ```
      > dataDir=/home/ubuntu/kafka/zookeeper
        ...
      > initLimit=5
      > syncLimit=2
      > server.1=172.31.45.58:2888:3888
      > server.2=...
      ```
  - ```$ echo 1 > ~/kafka/zookeeper/my (zookeeper id 번호 만들어주기)```
- kafka, zookeeper service 등록 스크립트 작성
  - ```$ sudo vi /etc/systemd/system/zookeeper.service```
    - ```
      [Unit]
      Requires=network.target remote-fs.target
      After=network.target remote-fs.target

      [Service]
      Type=simple
      User=ubuntu
      ExecStart=/home/ubuntu/kafka/bin/zookeeper-server-start.sh /home/ubuntu/kafka/config/zookeeper.properties
      ExecStop=/home/ubuntu/kafka/bin/zookeeper-server-stop.sh
      Restart=on-abnormal

      [Install]
      WantedBy=multi-user.target
      ```
  - ```$ sudo vi /etc/systemd/system/kafka.service```
    - ```
      [Unit]
      Requires=zookeeper.service
      After=zookeeper.service

      [Service]
      Type=simple
      User=ubuntu
      ExecStart=/bin/sh -c '/home/ubuntu/kafka/bin/kafka-server-start.sh /home/ubuntu/kafka/config/server.properties > /home/ubuntu/kafka/kafka.log 2>&1'
      ExecStop=/home/ubuntu/kafka/bin/kafka-server-stop.sh
      Restart=on-abnormal

      [Install]
      WantedBy=multi-user.target
      ```
- Service 실행
  - ```$ sudo systemctl daemon-reload```
  - ```$ sudo systemctl start kafka```
  - ```$ sudo systemctl status kafka```
  - ```$ sudo systemctl enable zookeeper```
  - ```$ sudo systemctl enable kafka```
  - ```
    cluster 오류가 발생할 경우, kafka/logs/meta.properties를 지워보고 실행해보자 !
    ```
- Topic 생성
  - ```$ kafka/bin/kafka-topics.sh --create --zookeeper 172.31.45.58:2181 --replication-factor 1 --partitions 1 --topic fastcampus```
- Topic List 확인
  - ```$ kafka/bin/kafka-topics.sh --list --zookeeper 172.31.45.58:2181```
- Topic의 Desc
  - ```$ kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic fastcampus --describe```
    - ```
      Topic: fastcampus       PartitionCount: 1       ReplicationFactor: 1    Configs: segment.bytes=1073741824
        Topic: fastcampus       Partition: 0    Leader: 0       Replicas: 0     Isr: 0
      ```
- Message Producing
  - ```$ kafka/bin/kafka-console-producer.sh --broker-list 172.31.45.58:9092 --topic fastcampus```
- Message Consuming
  - ```$ kafka/bin/kafka-console-consumer.sh --bootstrap-server 172.31.45.58:9092 --topic fastcampus --from-beginning```
























