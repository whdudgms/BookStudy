# 1장  마이크로서비스 아키텍처와 Redis

---

## 1.  NoSQL

- **정의**: 관계형 DB가 아닌 비정형 데이터 저장소
- **종류**:  
  - Key-Value: Redis  
  - Document: MongoDB  
  - Column: Cassandra  
  - Graph: Neo4j
- **특징**:
  - 스키마 유연성
  - 수평 확장에 강함
  - 빠른 읽기/쓰기 성능
- **Redis**:
  - 대표적인 In-memory Key-Value NoSQL
  - 캐시, 세션 관리, 실시간 처리에 최적화

---

## 2.  모놀리틱 아키텍처 (Monolithic Architecture)

- **구조**: 하나의 큰 애플리케이션으로 모든 기능이 통합됨
- **장점**:
  - 개발 초기에는 빠르게 시작 가능
  - 배포/테스트가 단순
- **단점**:
  - 하나의 기능 수정 → 전체 재배포 필요
  - 확장성 및 유지보수가 어려움
  - 팀 간 작업 충돌 가능성 ↑

---

## 3.  마이크로서비스 아키텍처 (MSA)

- **구조**: 기능별로 분리된 작은 독립 서비스들의 집합
- **장점**:
  - 독립 배포 가능 → 배포 속도 향상
  - 개별 확장 가능 → 성능 최적화 용이
  - 팀별로 서비스 분담 가능
  - 서비스마다 다른 기술 스택 사용 가능
- **단점**:
  - 서비스 간 통신 복잡 (REST, gRPC 등)
  - 트랜잭션 처리 어려움
  - 모니터링, 장애 대응 등 운영 복잡도 증가
- **Redis 활용**:
  - 서비스 간 빠른 통신 (Pub/Sub)
  - 공통 캐시 저장소
  - 세션 공유
  - 작업 큐 및 임시 데이터 처리

---

## 4.  데이터 저장소 요구사항의 변화

### 모놀리틱 → MSA로 전환 시:

- **변화된 요구 사항**:
  - 단일 DB → 각 서비스 별 분산 DB
  - 트랜잭션 중심 → Eventually Consistent 전략
  - 대용량 처리와 빠른 응답 시간 필요
  - 복구와 확장을 위한 유연한 구조 필요

### Redis와 같은 NoSQL이 필요한 이유:

- 빠른 응답 시간 제공 (In-memory)
- 빈번한 읽기/쓰기 작업에 유리
- 중앙 캐시 또는 메시지 브로커 역할 가능
- 수평 확장이 용이

---

## 5.  Redis란?

- **정의**: In-memory 기반의 Key-Value 구조 NoSQL 데이터 저장소  
- **특징**:
  - 메모리 기반 → 매우 빠른 읽기/쓰기 성능 (마이크로초 단위)
  - 다양한 자료구조 지원: `String`, `List`, `Set`, `Hash`, `Sorted Set`, `Stream`
  - 영속성(Persistence) 지원: RDB, AOF 옵션
  - 고가용성 구성 지원: Redis Sentinel, Redis Cluster
- **용도**:
  - 캐시 (가장 흔한 사용 예)
  - 세션 저장소
  - Pub/Sub 메시징 시스템
  - 순위 시스템, 큐 시스템, 일시적인 데이터 처리

---
<div style="display: flex; align-items: center; margin-bottom: 20px;">
  <div style="flex: 1; padding-right: 10px;"><strong>성능</strong></div>
  <div style="flex: 1;">
    <img src="https://github.com/user-attachments/assets/a8e64c05-39cd-4e17-8a30-9ed263a8aac4" width="100%" />
  </div>
</div>

<div style="display: flex; align-items: center; margin-bottom: 20px;">
  <div style="flex: 1; padding-right: 10px;"><strong>단순성 (Key-Value)</strong></div>
  <div style="flex: 1;">
    <img src="https://github.com/user-attachments/assets/17fc926d-cb06-4b8d-b13a-2bcefb32a7a2" width="100%" />
  </div>
</div>

<div style="display: flex; align-items: center; margin-bottom: 20px;">
  <div style="flex: 1; padding-right: 10px;"><strong>확장성</strong></div>
  <div style="flex: 1;">
    <img src="https://github.com/user-attachments/assets/55e65854-5c6e-4c1c-b862-fa9e671da789" width="100%" />
  </div>
</div>

<div style="display: flex; align-items: center; margin-bottom: 20px;">
  <div style="flex: 1; padding-right: 10px;"><strong>이벤트 루프 기반 싱글 스레드</strong></div>
  <div style="flex: 1;">
    <img src="https://github.com/user-attachments/assets/baf997cd-cbe5-4c85-b675-43cb6880aa0f" width="100%" />
  </div>
</div>



## 6.  왜 Redis가 필요한가?

### 1. **성능 (Performance)**
- 디스크 기반 DB보다 수천 배 빠름  
- 실시간 응답 요구되는 서비스에 필수 (예: 실시간 랭킹, 알림, 캐시 등)

### 2. **확장성 (Scalability)**
- 수평 확장 가능한 구조 (클러스터링, 샤딩)
- MSA에서 서비스별 부하 분산 가능

### 3. **데이터 분리 및 비동기 처리**
- 장기 저장이 아닌, 임시 데이터 또는 큐 처리에 적합  
- Redis를 통해 DB 트래픽 감소 가능 (Hot data 캐싱)

### 4. **분산 환경에서의 공유 상태 관리**
- 여러 서비스 간 세션 공유, 토큰 관리 등 공통 데이터 저장 가능

### 5. **다양한 활용 예**
- 세션 저장소 (Spring Session, Express-session 등과 연동)
- 인증 토큰 저장소 (ex. JWT 블랙리스트 관리)
- 메일/문자인증 코드 임시 저장
- API 호출 제한 (Rate Limiting)

---

## ✅ Redis를 언제 쓰면 좋은가?

| 사용 시나리오         | 설명                              |
|----------------------|-----------------------------------|
| 캐시(Cache)          | 느린 DB 조회 결과를 Redis에 저장하여 성능 향상 |
| 세션(Session)        | 로그인 세션 상태를 공유할 때 사용   |
| 순위 시스템          | Sorted Set을 이용한 랭킹 처리      |
| 작업 큐(Queue)       | List 구조를 이용한 비동기 작업 처리 |
| 실시간 알림/채팅     | Pub/Sub 기능을 활용한 메시징      |
| 임시 인증/검증 코드  | TTL을 활용한 일회성 인증코드 저장 |

## ✅ 요약

| 항목 | 모놀리틱 | 마이크로서비스 |
|------|----------|----------------|
| 구조 | 통합형 | 분산형 |
| 확장성 | 낮음 | 높음 |
| 배포 | 전체 재배포 | 개별 배포 |
| 데이터베이스 | 단일 RDB | 서비스 별 DB, NoSQL 혼합 |
| Redis 역할 | 적음 | 큐, 캐시, 세션 등 핵심 역할 |

