# MODI 렌탈 백엔드

상품 대여 플랫폼을 위한 마이크로서비스 기반 백엔드 프로젝트입니다.
회원, 판매자, 상품, 렌탈, 지갑/결제, 배송/고객지원, AI 기능을 여러 서비스로 분리해 구성했습니다.

---

## 프로젝트 소개

`COC-rental`은 `modi/` 디렉터리 아래에 구성된 Spring Boot 멀티 모듈 백엔드입니다.

서비스 디스커버리, 중앙 설정 서버, API 게이트웨이, 공통 모듈, 도메인별 서비스, Kafka 이벤트 처리, Docker/Kubernetes 배포 리소스를 포함하고 있습니다.

---

## 주요 기능

- 회원가입, 로그인, JWT 인증, OAuth2 계정 연동, 이메일 인증
- 판매자 등록 및 판매자 상태 관리
- 상품 등록, 상품 검색, 인기 상품/검색어 통계
- 장바구니 기반 렌탈 생성 및 단건 렌탈 생성
- 렌탈 승인, 거절, 결제, 취소, 반납, 환불, 연장 처리
- 상품별 대여 불가능 날짜 조회
- 지갑 잔액, 예치금 충전, 렌탈 결제/환불 트랜잭션 처리
- 배송, 리뷰, 공지, 블랙리스트, 관리자 기능
- AI 기반 상품 추천, 상품 설명 생성, 리뷰 요약, 상품 검수, 임베딩 처리
- Kafka 기반 이벤트 처리와 Outbox 스타일 발행 구조
- Spring Cloud Gateway, Eureka, Config Server 기반 MSA 구성
- Docker Compose 및 Kubernetes/Kustomize 배포 리소스 제공

---

## 기술 스택

| 구분 | 기술 |
| --- | --- |
| Language | Java 17 |
| Framework | Spring Boot 3.5.8, Spring Cloud 2025.0.0 |
| Architecture | MSA, Spring Cloud Gateway, Eureka, Spring Cloud Config |
| Data | PostgreSQL, Redis, pgvector, QueryDSL, JPA |
| Messaging | Kafka, Zookeeper, Outbox Event Flow |
| AI | Spring AI 1.0.0-M5, OpenAI 호환 Chat/Embedding Client |
| Security | Spring Security, JWT, OAuth2, Internal API Token |
| Observability | Actuator, Prometheus Registry, Logstash Encoder, ELK 리소스 |
| Deployment | Docker Compose, Kubernetes, Kustomize, Tilt |
| Docs / API | springdoc-openapi, API 명세 문서 |

---

## 서비스 모듈

| 모듈 | 역할 |
| --- | --- |
| `member-service` | 회원 프로필, 인증, 이메일 인증, OAuth2, 권한 |
| `seller-service` | 판매자 등록, 판매자 상태, 정산 지원 |
| `product-service` | 상품 카탈로그, 검색, 통계, 인기 데이터 |
| `rental-service` | 장바구니, 렌탈 생명주기, 결제/환불 연동 |
| `account-service` | 지갑, 예치금, 렌탈 결제/환불 트랜잭션 |
| `support-service` | 배송, 리뷰, 공지, 관리자, 블랙리스트 |
| `ai-service` | 추천, 설명 생성, 검수, 임베딩, 리뷰 요약 |
| `modi-gateway` | 게이트웨이 라우팅, 인증 헤더 주입, API 진입점 |
| `modi-discovery` | Eureka 서비스 디스커버리 |
| `modi-config` | 중앙 설정 서버 |
| `common-*` | 보안, 로깅, Kafka, 알림, OpenAPI, 공통 도메인 모듈 |

---

## 아키텍처 특징

- 비즈니스 기능을 서비스 단위로 나누고, 공통 관심사는 `common-*` 모듈로 분리
- 외부 요청은 `modi-gateway`를 통해 진입하고 서비스 ID 기준으로 라우팅
- 게이트웨이에서 인증된 사용자 정보를 하위 서비스로 헤더 주입
- 내부 API는 `X-Internal-Token`으로 서비스 간 호출 보호
- 렌탈/결제/환불 흐름은 rental, account, product, seller, support 도메인이 협력
- Kafka와 Outbox 모듈을 활용해 비동기 도메인 이벤트 처리
- Docker Compose와 Kubernetes 리소스로 로컬/배포 환경 구성 가능

---

## 실행 방법

```bash
cd modi

# 전체 모듈 빌드
./gradlew build

# 테스트 실행
./gradlew test
```

루트의 `docker-compose.yml`에는 PostgreSQL, Redis, Kafka/Zookeeper, Elasticsearch, Discovery, Config, Gateway, 도메인 서비스 컨테이너 구성이 포함되어 있습니다.

```bash
# 저장소 루트에서 실행
docker compose up -d
```

---

## API 문서

- [루트 API 명세](API_SPEC.md)
- [MODI API 요약](modi/docs/api-spec.md)
- [DB ERD](modi/docs/db-erd.md)

게이트웨이 기준 API 경로는 다음 형태를 따릅니다.

```text
/{service-id}/api/**
/{service-id}/internal/**
```

---

## 프로젝트 구조

```text
.
├── docker-compose.yml
├── API_SPEC.md
└── modi
    ├── account-service
    ├── ai-service
    ├── common
    ├── common-kafka
    ├── common-logging
    ├── common-notification
    ├── common-openapi
    ├── common-security
    ├── k8s
    ├── member-service
    ├── modi-config
    ├── modi-discovery
    ├── modi-gateway
    ├── product-service
    ├── rental-service
    ├── seller-service
    └── support-service
```
