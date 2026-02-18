# Ecommerce Scaling - 점진적 확장 포트폴리오

DAU 1,000에서 100만까지, 이커머스 시스템을 9단계로 점진적 확장하는 프로젝트.

각 Phase마다 **부하테스트로 병목을 증명하고, 인프라/코드 변경으로 해결한 뒤, Before/After 수치로 개선을 검증**한다.
모든 인프라는 Docker Compose로 로컬 실행 가능.

---

## 아키텍처 진화

```
Phase 1  [DAU 1K]     App + MySQL (단일 서버)
Phase 2  [DAU 5K]     App + MySQL (리소스 분리)
Phase 3  [DAU 5K]     App + MySQL + MinIO (이미지 분리)
Phase 4  [DAU 2만]    Nginx + App×2 + MySQL + MinIO (서버 이중화 + JWT)
Phase 5  [DAU 2만]    Nginx + App×2 + MySQL + MinIO + Redis (캐시 + 분산락)
Phase 6  [DAU 10만]   위 + CDN Nginx (이미지 캐싱)
Phase 7  [DAU 10만]   위 + MySQL Replica (읽기 분산)
Phase 8  [DAU 10만]   위 + Kafka + Worker (비동기 처리, 멀티모듈)
Phase 9  [DAU 100만]  위 + Vitess (DB 샤딩)
```

## 핵심 접근 방식

1. **병목 증명** - 부하테스트(k6)로 현재 아키텍처의 한계를 수치로 확인
2. **해결** - 인프라 또는 코드 변경으로 병목 해소
3. **검증** - 동일 부하에서 Before/After 메트릭 비교 (p95 레이턴시, RPS, 에러율)
4. **문서화** - Grafana 대시보드 스크린샷 + 수치 비교표로 기록

## 기술 스택

| 영역 | 기술 |
|------|------|
| Application | Java 17, Spring Boot 3.5, Gradle, JPA, QueryDSL |
| Infrastructure | Docker Compose, MySQL 8.4, Nginx, Redis, Kafka, Vitess |
| Monitoring | Prometheus, Grafana, Micrometer, k6 |
| Documentation | SpringDoc OpenAPI (Swagger UI) |

## 실행 방법

```bash
# 특정 Phase로 이동
git checkout phase-XX-complete

# 인프라 실행
docker compose up -d --build

# API 문서 확인
open http://localhost:8080/swagger-ui.html

# 모니터링
open http://localhost:3000  # Grafana (admin/admin)

# 부하테스트
k6 run --env VU=50 --env DURATION=10m k6/load-test.js
```
