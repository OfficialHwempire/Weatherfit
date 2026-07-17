# WeatherFit — Agent 가이드

## 프로젝트 개요

날씨(위치·체감 온도)와 사용자의 옷장 데이터를 기반으로 옷차림을 추천하고, 피드·팔로우·DM·알림을 제공하는 패션 추천 소셜 플랫폼. 상세 소개와 전체 기술 스택은 `README.md` 참고.

- **Core**: Java 21, Spring Boot 3.5, Gradle
- **데이터**: PostgreSQL(운영), H2(로컬/테스트), Redis(캐시·ShedLock·알림), Elasticsearch(피드 검색), JPA + QueryDSL
- **인프라/연동**: Kafka(메시지), WebSocket/STOMP, SSE, AWS S3, Spring Batch(날씨 수집), Spring AI(OpenAI 추천)
- **설정 프로파일**: `application-dev.yml` / `application-docker.yml` / `application-prod.yml`

## 빌드와 테스트

- 작업 완료 전에 반드시 컴파일과 테스트를 통과시킨다.
  - 빌드: `./gradlew build` (Windows: `gradlew.bat build`)
  - 테스트: `./gradlew test` — JUnit 5, 테스트 후 JaCoCo 리포트가 자동 생성됨(`build/reports/jacoco`)
- 로컬 인프라(Redis, Kafka, Elasticsearch)는 `docker-compose up -d`로 기동한다.

## 패키지 구조

`src/main/java/com/codeit/weatherfit` 아래 도메인형 구조를 따른다. 새 코드는 반드시 해당 도메인 패키지에 위치시킨다.

- `domain/{auth, clothes, feed, follow, message, notification, profile, recommendation, user, weather}`
  - 각 도메인 내부: `controller`(+`controller/docs` Swagger 인터페이스), `service`, `repository`, `entity`, `dto/request`, `dto/response`, `exception`, `event`
- `global/` — 공통 설정(`config`), 보안(`security`), 예외(`exception`), S3, 유틸

## 브랜치 / 커밋 컨벤션

- 기본 브랜치는 `dev`이며 PR도 `dev`를 대상으로 한다. `dev`에 직접 커밋하지 않고 작업 브랜치에서 PR로 병합한다.
- 커밋 메시지는 `feat:`, `fix:` 등 타입 접두사 + 한글 설명 형식을 따른다. (예: `feat: 알림 중복 전송 수정`)

## 수정 금지

- `.env`, `.env.local`의 값은 절대로 임의로 수정하지 않는다.
- `.github/workflows/`의 CI/CD 설정은 사용자의 요청 없이 수정하지 않는다.

## 테스트 파일 임의 수정 금지

테스트 파일을 수정하기 전에는 항상 사용자의 허락을 받는다.

## 로그 규칙

로그는 반드시 Lombok의 `@Slf4j`를 이용해 남긴다. `System.out.println`은 사용하지 않는다.

## Swagger 문서 동기화

- Controller 코드를 수정하면 대응하는 Swagger Docs 인터페이스(`controller/docs/*ApiDocs`)에도 해당 변경을 반드시 반영한다.
- 대응하는 `*ApiDocs` 인터페이스가 없으면 새로 생성하되, 생성 전에 사용자의 허락을 받는다.
