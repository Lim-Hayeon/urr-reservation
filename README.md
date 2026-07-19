<img width="706" height="336" alt="image" src="https://github.com/user-attachments/assets/1212a6a1-9d2a-4f91-96c0-9206a5627664" /># 🍽️ URR - 우르르 다같이 예약하기

여러 명이 함께 가는 모임 예약을, 인원수에 맞는 메뉴·최소주문 규칙까지 한 번에 관리하는 그룹 예약 서비스입니다.

## 📖 소개

친구들, 동아리, 회사 회식처럼 **여러 명이 함께 가는 자리**를 예약할 때, 매장마다 다른 인원수별 최소주문 규칙과 메뉴 구성을 일일이 확인하기 번거로운 문제를 해결하기 위한 프로젝트입니다. 매장의 예약 가능한 시간대(slot)를 확인하고, 인원수에 맞는 최소주문 규칙을 자동으로 안내하며, 메뉴를 선택해 예약까지 한 번에 진행할 수 있습니다.

## ✨ 주요 기능

- 🏪 **매장 조회** — 매장 목록 및 상세 정보(시간대별 예약 현황 포함) 조회
- 🕐 **시간대(Slot) 기반 예약** — 매장별로 열려있는 예약 가능 시간대 확인 후 예약
- 🍱 **메뉴 선택 및 필수 메뉴 처리** — 매장별 메뉴 목록, 필수로 포함되어야 하는 메뉴(`is_required`) 구분
- 💰 **인원수 구간별 최소주문 규칙** — 인원수에 따라 다른 최소주문금액 자동 적용 (예: 1~5명 5만원, 6~15명 10만원 ...)
- ❌ **예약 취소** — 예약 건 취소 처리
- 🛠️ **관리자 페이지** — 전체 예약 목록을 한눈에 보는 admin 대시보드
- 🔗 **Notion 연동** — 예약 데이터를 Notion과 동기화 (`NotionSyncService`)

## 🛠 Tech Stack

| 분류 | 스택 |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 4.0.4, Spring Web MVC |
| Database | Spring Data JPA + H2 (in-memory) |
| Build | Gradle |
| 기타 | Lombok, Bean Validation, H2 Console |

## 📁 프로젝트 구조

```
reservation/  
└── src/main/java/com/hayeon/reservation/  
    ├── controller/         # API 엔드포인트  
    ├── service/            # 비즈니스 로직 (예약, 매장, Notion 동기화)  
    ├── entity/             # Store, Slot, Menu, Reservation, MinOrderRule 등  
    ├── repository/         # JPA Repository  
    ├── dto/                # 요청/응답 DTO (store, reservation, admin)  
    ├── common/             # 공통 응답 포맷, 예외 처리  
    └── config/             # CORS, Async 설정  
 ```

### 도메인 모델

```text
Store (매장)
 ├── Slot (예약 가능 시간대)
 ├── Menu (메뉴, 필수 여부 포함)
 └── MinOrderRule (인원수 구간별 최소주문 규칙)

Reservation (예약)
 └── ReservationMenu (예약에 포함된 메뉴 목록)
```

## 🔌 API

| Method | Endpoint | 설명 |
|---|---|---|
| GET | `/api/stores` | 매장 목록 조회 |
| GET | `/api/stores/{storeId}` | 매장 상세 조회 |
| POST | `/api/reservations` | 예약 생성 |
| GET | `/api/reservations/check` | 예약 확인 |
| PATCH | `/api/reservations/{reservationId}/cancel` | 예약 취소 |
| GET | `/api/reservations/admin/list` | (관리자) 전체 예약 목록 조회 |
| GET | `/admin.html` | 관리자 대시보드 페이지 |

## 🚀 실행 방법

```bash
cd reservation
./gradlew bootRun
```

서버 실행 후:
- API: `http://localhost:8080/api/...`
- 관리자 페이지: `http://localhost:8080/admin.html`
- H2 콘솔: `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:mem:testdb`, username: `sa`)

실행 시 `data.sql`을 통해 매장 3곳(한식당, 피자 하우스, 스시 오마카세) 샘플 데이터가 자동으로 세팅됩니다.

## 📝 참고

- DB는 애플리케이션 재시작 시 초기화되는 인메모리 H2를 사용합니다 (`ddl-auto=create-drop`).
- 타임존은 `Asia/Seoul`로 고정되어 있습니다.
