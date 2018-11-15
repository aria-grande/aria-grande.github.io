---
layout: post
title:  "네이버에서 사용되는 여러가지 Data Platform, 그리고 MongoDB"
author: Aria
category: "Deview 2018"
comments: true
---
[발표자료](https://www.slideshare.net/deview/124-data-platform-mongodb)

## 네이버에서의 Data Platform
- 초창기에는 거의 모든 서비스가 2-tier 구조(Web server-RDBMS server)
- 서비스가 점점 커지면서, 빠른 응답을 보장하기 위해 cache 추가(Redis, ARCUS)
- 지표를 저장하기에는 RDBMS는 sharding이 되지 않아 공간과 비용적 문제가 발생. 원활한 데이터 처리를 위해 HBase(Hadoop) 사용 중.

## MongoDB가 네이버에서 어떤 경우 대안이 되고 있는가?
- 2.0 지원 Coverage 확대
- Sharding, Transaction 처리, secondary index처리가 가능하고, schema-less한 DB가 필요했다.
### Schema-less
- 사전에 데이터에 대한 구조나 정의를 하지 않아도 됨.
- RDBMS라면 각 서비스 별 별도의 table로 관리해야 하거나, column 추가/삭제 등의 서비스 중 overhead가 발생하게 된다.

```
table A{
  userNumber int
  ,userCellPhoneNumber char(13)
}

-> RDBMS: 1 row 당 17-byte
-> MongoDB: 1 document 당 46-byte. 컬럼 key-value가 같이 저장되기 때문에 저장 공간이 많이 필요하다.
```

공통플랫폼: 서비스 별 비슷한 기능을 공통 플랫폼에서 제공하는데, 이 때 schema-less 하면 좋다.

### Sharding
- scale up의 한계가 대두됨. RDBMS는 응용에서 샤딩으로 scale out 구현 할 수 있지만 확장성이 떨어지며 개발 및 관리의 overhead가 발생한다. Auto sharding이 가능한 데이터 플랫폼이 필요하다.

### Secondary Index
- 단순 bigdata성 데이터는 HBase를 사용하고 있음.(인프라 운영비용 절감)
- HBase는 제품 자체에서 secondary index를 제공해주지 않는데, 조회 조건이 다양해짐에 따라 secondary index에 대한 기능이 필요해짐.
- HBase는 HFile이 물리적으로 분리됨. MongoDB Chunk는 논리적 분리

### Transaction
- NoSQL을 사용하면서 Transaction 기능을 원하는 경우가 있음.
- MongoDB는 WiredTiger storage 엔진을 사용할 경우 높은 수준의 ACID 지원
```
s.start_transaction()
  order.blahblah
  payment.blahblah
s.commit_transaction()
```

### JSON 지원
- REST API 사용이 확대되면서, 메세지 포맷을 JSON으로 사용할 경우
- Web server와 DB server 간 데이터를 주고 받는 과정에서 JSON이 선호되면서 JSON 지원이 필요해짐.

### IDC DR(Disaster Recovery: 이중화)
- MongoDB는 IDC간 Auto failover가 가능한 Data Platform
- MongoDB는 IDC를 꼭 3대로 유지해야 함.


## 네이버에서 MongoDB를 사용하면서 겪은 에피소드들
### Mongos 관리
- 웹 서버는 Mongos(router)와 통신을 하게 되고, mongos가 각 shard server에 질의를 한다.

### L4와 getmore
- Sharding 에서의 L4 사용을 초기에는 권장했으나(Web server -> L4 -> mongos -> sharding server) getmore 이슈로 제거.
(getmore: 20건 단위로 데이터를 전달하는 과정. 100개를 질의한다면 처음 20개 질의, 그 다음 20개 질의 ...)

### mongos <-> shard 커넥션
- mongos와 shard server 사이에 커넥션 관리에 문제가 있음.

```
default connection 갯수: Max-> Unlimited, Min-> 1개(per core)
```
- 요청이 갑자기 늘어나면 해당 DB 서버는 장애로 빠질 영향이 크다.
고로 튜닝이 필요하다.

```
tuning: Max -> 20개(per core), Min -> 10개(per core)로 네이버에서 사용중.
```
- Mongos와 shard 사이에 커넥션이 급증하는 경우는 아직까지 많지 않았음. 몇 초 이내에 해결.

### Node.js driver
- Node.js 모듈인 mongoose는 성능 이슈로 인해 권장하지 않음.
- Node.js 드라이버 2.X 사용 권장

### Storage Engine
- WiredTiger 스토리지 엔진만 사용.
- checkpoint: memory buffer와 disk 사이의 데이터 불일치를 해소하기 위해 data sync하는 작업(memory -> disk)
- checkpoint가 실행되는 시점에 한번에 디스크 쓰기 발행. Write heavy한 환경에서 checkpoint시 성능 하락. 주기적으로 Disk IO가 높아진다면 checkpoint 이슈일 수도 있음.

### Background Index
- MongoDB는 인덱스 생성시 collection 뿐만 아니라 database 자체에 lock이 걸린다. 쿼리 지연 발생.
- background index 생성 가능.(=RDBMS의 online index creation)
- background index 생성은 주로 새벽에 진행

### Compact
- compact는 maintenance 기간에만 사용할 것.

### Balancer
- MongoDB의 Chunk Migration은 high cost.
여러 이유로 balancing 작업이 실패할 수 있음.

### Clustered Index
- clustered index: 해당 key 값 기준으로 실제 데이터가 정렬되어 있음.
- clustered index 방식을 활용한 범위 검색 등에는 MongoDB 사용을 권장하지 않는다.

### Unique index
- sharding 환경에서 unique index는 local index라 shard 내에서만 unique 함. 전체 shard 내에서는 유니크 하지 않다.
- 전체 shard에서 유니크함을 보장하기 위해서는 sharding key에 포함 필요.

### 개인정보
- DB영역의 법적 요건(IP 기반 access-control)을 지키기 위해서 MongoDB 3.6 버전의 authenticationRestrictions 기능 사용 중.
bind_ip는 DB ACL을 제한하는 기능이 아님. whitelist임.

## 개인적으로 바라보는 MongoDB의 장단점 그리고 전망(vs RDBMS)
### 과연 NoSQL 인가?
- MongoDB는 NoSQL의 특징과 RDBMS의 특징을 고루 가지고 있는 DBMS.

### 성능과 안정성
- checkpoint 등 일부 성능 문제 해결이 시급
- Mongos <-> shard사이의 커넥션 안정화

### 전망
- 우리나라에서 Main stream으로 도입될 가능성은 높지 않다고 보여지나, 사용처는 점점 확대될 것으로 보인다.
