# ALBA24 - 점주와 알바생을 위한 근무관리 솔루션

> 자영업자와 아르바이트 근로자를 위한 올인원 근무관리 웹 플랫폼

---

## 프로젝트 소개

소규모 사업장의 복잡한 근무 형태에 대응하지 못하는 기존 근태관리 서비스의 한계를 해결하기 위해 개발된 웹 기반 근무관리 솔루션입니다.

점주(매니저)와 알바생이 하나의 플랫폼에서 출퇴근 관리, 근무 데이터 시각화, 재고 관리, 실시간 채팅을 모두 이용할 수 있습니다.

---

## 주요 기능

**공통**
- 이메일 기반 회원가입 / 로그인 (매니저 / 알바생 권한 분리)
- 실시간 그룹 채팅 (WebSocket 기반)
- 재고 관리 (등록 / 수정 / 삭제)

**매니저 전용**
- 직원 목록 및 출퇴근 현황 조회
- 채팅방 멤버 추가 / 강제 퇴장 (퇴직자 처리)
- 인증코드 발급을 통한 알바생 회원가입 관리

**알바생 전용**
- GPS 기반 출퇴근 체크 (지정 위치에서만 가능)
- 근무 시간 시각화 (일별 / 주별 / 월별 차트)
- 근무 스케줄 등록 및 확인

---

## 기술 스택

| 구분 | 기술 |
|------|------|
| Frontend | React.js, Axios, styled-components, Recoil |
| Backend | Spring Boot 2.7.5, Spring Security, JPA |
| Database | MySQL 8.0 |
| 인증 | JWT (JSON Web Token) |
| 실시간 통신 | WebSocket (STOMP) |
| 빌드 도구 | Gradle 7.5.1 |
| 개발 환경 | IntelliJ IDEA, Java 17 |

---

## 프로젝트 구조

```
testing/
├── src/
│   └── main/
│       ├── java/caps/testing/
│       │   ├── config/          # Security, WebSocket 설정
│       │   ├── controller/      # REST API 컨트롤러
│       │   ├── domain/          # JPA 엔티티
│       │   ├── dto/             # 요청/응답 DTO
│       │   ├── jwt/             # JWT 토큰 처리
│       │   ├── repository/      # JPA 레포지토리
│       │   ├── service/         # 비즈니스 로직
│       │   └── role/            # 권한 관리
│       ├── frontend/            # React 프로젝트
│       │   └── src/
│       │       ├── pages/       # 페이지 컴포넌트
│       │       ├── components/  # 공통 컴포넌트
│       │       └── recoil/      # 전역 상태 관리
│       └── resources/
│           └── application.yml  # 환경 설정
└── build.gradle
```

---

## 시작하기

### 사전 요구사항

- Java 17
- MySQL 8.0
- Node.js / npm

### DB 설정

MySQL에 접속 후 아래를 실행합니다.

```sql
CREATE DATABASE capstone CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'capstone'@'localhost' IDENTIFIED BY '비밀번호';
GRANT ALL PRIVILEGES ON capstone.* TO 'capstone'@'localhost';
FLUSH PRIVILEGES;
```

### application.yml 설정

`src/main/resources/application.yml` 에서 DB 접속 정보를 수정합니다.

```yaml
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/capstone?serverTimezone=UTC
    username: capstone
    password: 비밀번호
  jpa:
    hibernate:
      ddl-auto: update   # 최초 실행 시 create, 이후 update
```

### 실행 방법

**백엔드 실행**

```bash
cd testing
./gradlew bootRun
```

**프론트엔드 실행 (개발 모드)**

```bash
cd testing/src/main/frontend
npm install
npm start
```

| 서비스 | 주소 |
|--------|------|
| Spring Boot | http://localhost:8080 |
| React 개발 서버 | http://localhost:3000 |

### 회원가입 순서

1. `http://localhost:3000/registermanager` 에서 매니저 계정 생성
   - 인증코드는 본인이 직접 설정 (알바생 가입 시 사용)
   - 지점주소는 정확한 도로명 주소 입력
2. `http://localhost:3000/registerworker` 에서 알바생 계정 생성
   - 매니저가 설정한 인증코드 입력 시 동일 지점에 자동 배정
3. `http://localhost:3000/login` 에서 로그인

---

## 데이터베이스 구조

| 테이블 | 설명 |
|--------|------|
| member | 회원 정보 (매니저 / 알바생) |
| team | 지점 정보 (이름, 주소, GPS 좌표) |
| work | 근무 기록 (출퇴근 시간, 근무 유형) |
| work_time | 근무 스케줄 |
| stock | 재고 관리 |
| room_entity | 채팅방 |
| message_entity | 채팅 메시지 |

---

## 팀원

| 학번 | 이름 |
|------|------|
| 2016112227 | 변성현 |
| 2016112260 | 고정빈 |
| 2017110436 | 허강현 |
| 2017112245 | 김상호 |

---

## 알려진 이슈

- vworld 주소 API 키 만료 시 지점 등록 시 위도/경도가 `0.0, 0.0` 으로 저장됩니다. 정상 동작을 위해 [vworld](https://www.vworld.kr) 에서 새 API 키를 발급받아 `MemberService.java` 의 `makeUrl()` 메서드에 교체해주세요.
- GPS 출퇴근 기능은 브라우저의 위치 정보 권한이 허용된 환경에서만 동작합니다.
