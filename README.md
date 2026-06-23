# Capstone-DB

**Masil** 프로젝트의 데이터베이스 설정 모듈입니다.  
한국관광공사 공모전 캡스톤 프로젝트로, Spring WebFlux 기반의 리액티브 아키텍처에서 PostgreSQL(pgvector)과 Redis를 연동합니다.

---

## 기술 스택

| 분류 | 기술                                 |
|------|------------------------------------|
| Language | Java 21                            |
| Framework | Spring Boot 3.5.13, Spring WebFlux |
| Database | PostgreSQL 16 + pgvector (R2DBC)   |
| Cache | Redis 7.2 (Reactive)               |
| Build | Gradle (Kotlin DSL)                |
| Container | Docker / Docker Compose            |

---

## 프로젝트 구조

```
src/main/java/com/masil/capstonedb/
├── CapstoneDbApplication.java       # 애플리케이션 진입점
└── config/
    ├── R2dbcConfig.java             # R2DBC + pgvector 설정
    └── RedisConfig.java             # Reactive Redis 설정 (Jackson 직렬화)
```

---

## 실행 방법

### 1. Docker로 인프라 실행

```bash
docker-compose up -d
```

| 서비스 | 컨테이너 | 포트 |
|--------|----------|------|
| PostgreSQL (pgvector) | `reactive-postgres-vector` | `5432` |
| Redis | `reactive-redis` | `6379` |

### 2. 애플리케이션 실행

```bash
./gradlew bootRun
```

서버가 `http://localhost:8080` 에서 시작됩니다.

---

## 환경 설정

`src/main/resources/application.yml`

```yaml
spring:
  r2dbc:
    url: r2dbc:postgresql://localhost:5432/mydb
    username: postgres
    password: masil1234
    pool:
      initial-size: 5
      max-size: 20

  data:
    redis:
      host: localhost
      port: 6379
```

> **주의:** 운영 환경에서는 비밀번호를 환경변수로 분리하세요.

---

## 주요 설정 설명

### R2dbcConfig
- `pgvector` 벡터 타입을 지원하는 R2DBC 커스텀 설정
- `ReactiveTransactionManager` 빈 등록으로 리액티브 트랜잭션 처리

### RedisConfig
- `ReactiveRedisTemplate<String, Object>` 빈 등록
- `Jackson2JsonRedisSerializer`로 JSON 직렬화
- `JavaTimeModule` 등록으로 Java 8 날짜/시간 타입 지원

---

## 의존성

```kotlin
// WebFlux
implementation("org.springframework.boot:spring-boot-starter-webflux")

// R2DBC (Reactive PostgreSQL)
implementation("org.springframework.boot:spring-boot-starter-data-r2dbc")
implementation("org.postgresql:r2dbc-postgresql")

// pgvector 벡터 타입 지원
implementation("io.github.pgvector:pgvector:0.1.6")

// Redis Reactive
implementation("org.springframework.boot:spring-boot-starter-data-redis-reactive")
implementation("io.lettuce:lettuce-core")
```
