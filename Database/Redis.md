# Redis(Remote Dictionary Storage)

### Redis의 기본 개념
- in-memory 기반의 key-value 형식 data 저장소
- 주로 캐싱을 위해 사용한다(Session Store, List Data Caching)
  - Data를 반복적으로 요구하거나, 빠른 입/출력이 필요한 경우 캐싱을 사용한다
- single thread로 초당 5~25만의 request의 실행이 가능하다
- 다양한 type의 아키텍처(single, master-slave, sentinel, cluster)를 지원한다
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/9bc15273-a8a4-4654-a152-dcc28b709679)
- Disk가 아닌 메모리에 데이터를 저장하기 때문에 휘발성이지만 AOF, RDB Snapshot을 통해 주기적으로 Disk에 저장하여 휘발성 문제를 해소할 수도 있따
- memory fragmentation이 발생해서 데이터 문제가 발생하기도 하고, Big Size Data에는 적합하지 않다
- Redis는 기간이 한정되어 있고, 빠른 반환이 필요한 부분에서 사용된다
  - 장바구니: 로그인과 동시에 캐싱 되거나 비 로그인 시 임시로 저장
  - 할인정보: 특정 기간과 특정 상품의 할인 정보
  - 토큰정보, 세션정보: 로그인 시 로그인 상태 유지를 위함
  - 채팅정보: 채팅 종료 시 까지 정보 유리
  - 광고: 사용자 별 상품 별 광고

### Redis vs MemCached
|               | Redis                                                  | MemCached     |
|---------------|--------------------------------------------------------|---------------|
| DataType      | Hash, List, Set, Sorted Set, String, Geo               | String        |
| Thread        | Single Thread                                          | Multi Threads |
| Scale         | ScaleOut                                               | Scale Up      |
| Memory Policy | no eviction, volatile-LRU, volatile-TTL, allkey-random | LRU           |
| Persistence   | AOF, RDB                                               | None          |
| ETC           | LUA Script                                             |               |


### Redis 설치 in Ubuntu
- Redis 설치
  - ```# apt-get update```
  - ```# apt-get install -y redis-server```
- 서비스 설치 확인
  - ```# service redis status```
  - ```# ps -ef | grep redis```
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/90ca8742-d7cf-4314-80fb-d8c9268a5672)
- redis config 확인
  - ```# redis-cli```
  - ```127.0.0.1:6379> info```
    - ```
      # Server
      redis_version:6.0.16
      tcp_port:6379
      config_file:/etc/redis/redis.conf
      ...

      # Replication
      role:master
      connected_slaves:0
      master_replid:80929a1c5114a9ed469112e545edceaaa6cee372
      ...

      # Cluster
      cluster_enabled:0
      ```
- redis.conf 설정 추가
  - ```# vi /etc/redis/redis.conf```
    - ```> bind 172.31.38.112 127.0.0.1 ::1```
    - ```> maxmemory 500mb```
  - ```# redis-cli config get maxmemory```
  - ```# service redis restart```
  - ```# ps -ef | grep redis```
- master redis에 대해 slave 생성
  - ```# replicaof 172.31.38.112 6379```
  - ```# tail -f /var/log/redis/redis-server.log```
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/2dc87397-d403-43f5-8686-a4cfa0790df9)
- redis master, slave config 확인
  - master: ```# redis-cli -h 172.31.45.28 -p 6379```
  - ```172.31.45.28:6379> info```
    - ```
      # Replication
      role:master
      connected_slaves:1
      slave0:ip=172.31.38.112,port=6379,state=online,offset=28,lag=1
      ```
  - slave: ```# redis-cli -h 172.31.38.112 -p 6379```
  - ```172.31.38.112:6379> info```
    - ```
      # Replication
      role:slave
      master_host:172.31.45.28
      master_port:6379
      ```
- 파일 연동 확인
  - ```172.31.45.28:6379> set fastcampus rediskafkalecture```
  - ```
    172.31.38.112:6379> get fastcampus
    "rediskafkalecture"
    ```
- redis-sentinel 설치
  - ```# apt-get install -y redis-sentinel```
- sentinel.conf 변경
  - ```# vi /etc/redis/sentinel.conf```
    - ```> bind 172.31.38.112 127.0.0.1 ::1```
    - ```> sentinel monitor mymaster 172.31.45.28(master ip) 6379 2```
  - ```# service redis-sentinel restart```
  - ```# service sentinel restart```
- sentinel 동작 확인
  - ```# tail -f /var/log/redis/redis-sentinel.log```
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/0ad2403e-9f53-4191-ba42-c797ea3dab9c)


### Redis Library 사용
- Jedis: 개발 복잡도가 낮다
- Luttuce: 다양한 옵션이 존재하여(advanced) scaling에 좋고, non-blocking reactive application에 유리하다
  - DataType
    - String(1:1관계), Lists(순서존재), Sets(순서와 관계없이 저장, 중복 불가)
    - Sorted Sets: ZSET이라고 불리며, Score 개념이 존재하여 정렬이 가능
    - Hashs: key:value 여러 커플이 존재하여 RDB의 table 개념으로 많이 사용됨
  - 명령어
    - ZADD: 입력
      - ```
        > zadd rtest 12000 p0001
        (integer) 1
        > zadd rtest 13000 p0002
        (integer) 1
        > zadd rtest 13500 p0003
        (integer) 1
        > zadd rtest 8500 p0004
        (integer) 1
        > zadd rtest 110000 p0005
        (integer) 1
        ```
    - ZCARD: count
      - ```
        > zcard rtest
        (integer) 5
        ```
    - ZRANGE: 내림차순 정렬순서로 조회
      - ```
        > zrange rtest 0 2
        1) "p0004"
        2) "p0001"
        3) "p0002"
        ```
      - ```
        > zrange rtest 0 1 withscores
        1) "p0004"
        2) "8500
        3) "p0001"
        4) "12000"
        ```
      - ```
        > zrangebyscore rtest 50000 150000 withscores
        1) "p0005"
        2) "110000"
        ```
    - ZREM: 삭제
      - ```
        > zrem rtest p0004
        ```
    - ZSCORE: 특정 member의 score를 조회
      - ```
        > zcore rtest p0003
        "13500"
        ```
    - ZRANK: 특정 member의 rank를 조회
      - ```
        > zrank rtest p0001
        (integer) 0
        > zrank rtest p0003
        (integer) 2
        > zrank rtest p0004
        (nil)
        ```

### Redis Stress Test
- 기본적으로 설치되어있는 Reids Benchmark를 이용하여 간단한 test가 가능하다
  - ```
    # redis-benchmark -h <hostname> -p <port> -c <clients_number> -n <request_count> -d <data_size(byte)>
    # redis-benchmark -h localhost -p 6379 -c 50 -n 100000 -d 3 
    ```

### Redis를 이용한 Session 관리
- 사용자를 식별하여 행위의 연속성이 생기도록 session을 관리한다
- 최초 로그인 시 sessionId가 없으면 이를 client에 부여하고, 부여된 session 정보를 header에 실어서 같은 사용자로 인식되도록 한다
  - sessionId, creationTime, accessTime, expirationTime, attribute 등의 정보를 저장
- Session 관리 방식
  - ![image](https://github.com/kimho1wq/TIL/assets/15611500/5307b9a7-8e68-4070-9fcc-71aa52f56c1e)
  - Sticky session 방식은 session을 공유하지 않기 때문에 계속되서 동일한 서버로 접근해야 한다
  - Session store을 사용하여 세션을 공유하며 사용해야 load balancing을 효율적으로 할 수 있다















 
