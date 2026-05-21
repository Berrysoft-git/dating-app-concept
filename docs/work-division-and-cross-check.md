# 문서 작업 분담 및 크로스 체크 계획

상태: 협업 진행 기준
작성일: 2026-05-21
기준 문서: `docs/collaboration-log.md`, `docs/service-overview.md`, `docs/prd.md`, `docs/mvp-scope.md`
대상 서비스: 프리미엄 검증 기반 AI 소개팅 서비스

## 1. 목적

이 문서는 제품/운영 담당과 개발/구현/보안 담당이 동시에 작성할 문서 범위를 분리하고, 양쪽 결과물이 끝난 뒤 어떤 기준으로 크로스 체크할지 정리한다.

원칙은 빠른 병렬 작업이다. 한쪽 문서가 다른 쪽 문서를 기다리며 막히지 않도록 담당 범위를 나누되, 최종 반영 전에는 정책, API/DB, 보안, UX 흐름이 서로 충돌하지 않는지 확인한다.

## 2. 역할 구분

| 역할 | 담당 범위 | 완료 기준 |
|---|---|---|
| 제품/운영/정책 담당 | 사용자 가치, 회원 정책, 검증 기준, 노출/매칭 원칙, 화면/운영 흐름 | 사용자가 보는 약속과 운영자가 따라야 할 기준이 문서화되어 있다. |
| 개발/구현/보안/아키텍처 담당 | API/DB, 저장소, 감사 로그, 권한, Capacitor 구현, 배치/삭제 작업 | 제품 정책을 실제 시스템으로 구현 가능한 구조가 문서화되어 있다. |

멘션 ID가 바뀌더라도 문서 책임은 역할 기준으로 유지한다.

## 3. 제품/운영 담당 문서

### P-001 인증자료 수집/검증/보관 명세서

파일: `docs/verification-evidence-lifecycle-spec.md`

작성할 내용:

- 사용자가 제출하는 인증자료 유형
- 업로드 전 고지와 동의 문구
- 자동 전처리, 악성 파일 검사, 메타데이터 제거 기준
- 관리자 열람 사유, 승인, 반려, 재제출 요청 흐름
- 승인/반려/철회/계정 삭제 시 원본 삭제 기준
- 사용자에게 보여줄 상태와 운영자 내부 상태 분리

크로스 체크 대상:

- `docs/privacy-verification-data-policy.md`
- 개발 측 `verification_evidence` DB/API 문서
- 관리자 감사 로그 문서

### P-002 노출 정책서

파일: `docs/exposure-policy.md`

작성할 내용:

- 일반 회원, 인기 일반 회원, 셀렉트/프리미엄 회원의 공개 범위
- 일반 회원의 셀렉트 추천 opt-in/철회 고지
- 셀렉트/프리미엄 회원 목록 비공개 원칙
- 추천 카드 생성 후 opt-out, 제한, 신고, 차단이 발생했을 때의 처리
- 뱃지, 검증 유형, 추천 이유에서 금지할 우열 표현

크로스 체크 대상:

- `docs/member-tier-policy.md`
- `docs/select-premium-verification-criteria.md`
- 개발 측 추천 후보군 API/DB 문서

### P-003 매칭 정책서

파일: `docs/matching-policy.md`

작성할 내용:

- 후보군 생성 원칙
- 필수 제외 조건: 차단, 신고 처리 중, 계정 제한, opt-in 미동의
- 인기 일반 신호의 사용 범위
- AI 추천 이유의 입력 제한과 출력 표현 기준
- 운영자 큐레이션이 개입하는 범위

크로스 체크 대상:

- `docs/prd.md`
- `docs/mvp-scope.md`
- 개발 측 Matching API, Recommendation API 문서

### P-004 사용자 플로우 및 화면 명세서

파일: `docs/user-flow-screen-spec.md`

작성할 내용:

- 가입, 프로필, 사진, 설문, opt-in, 검증 신청
- 심사 대기, 반려, 재제출, 철회
- 추천 카드, 추천 이유 상세, 수락/거절/숨김
- 신고/차단, 내 정보/노출 설정
- 각 화면의 빈 상태, 오류 상태, 권한 부족 상태

크로스 체크 대상:

- 개발 측 API 엔드포인트와 상태 전이
- Capacitor 파일/카메라/로컬 저장소 구현 문서

### P-005 관리자 심사/운영 정책서

파일: `docs/admin-review-operations-policy.md`

작성할 내용:

- 심사자, 운영자, 보안 관리자, 슈퍼 관리자 역할
- 인증자료 열람 사유 입력 기준
- 승인/반려/재제출 요청 사유 코드
- 내부 메모 작성 금지 표현
- 신고/제재와 검증 상태의 관계
- 감사 로그 조회와 변경 금지 원칙

크로스 체크 대상:

- 개발 측 관리자 권한 모델
- 감사 로그 저장/조회/변조 방지 문서

## 4. 개발/구현/보안 담당 문서

### D-001 API/DB 설계 초안

파일 제안: `docs/api-db-design-draft.md`

작성할 내용:

- `users`, `user_profiles`, `select_opt_ins`
- `verification_applications`, `verification_evidence`, `verification_reviews`, `verification_badges`
- `match_candidates`, `daily_recommendations`, `recommendation_explanations`, `match_actions`
- `user_blocks`, `contact_hash_blocks`, `reports`
- `admin_users`, `admin_audit_logs`
- 주요 enum, status, foreign key, unique constraint, idempotency key

제품 측 확인 포인트:

- 일반 회원 opt-in과 셀렉트 회원 목록 비공개가 DB/API 레벨에서 강제되는가
- 인증자료 원본과 민감 원문이 DB에 저장되지 않는가
- 등급, 계정 상태, 검증 상태, 노출 동의가 섞이지 않는가

### D-002 인증자료 저장소/삭제 작업 설계

파일 제안: `docs/verification-storage-deletion-design.md`

작성할 내용:

- 보안 저장소 객체 ID 구조
- 업로드 URL 발급과 만료
- 악성 파일 검사와 메타데이터 제거 처리 위치
- 삭제 대기열, 재시도, 실패 로그, 삭제 증적
- 승인 후 7일, 반려 후 14일, 철회 후 24시간 삭제 기준의 구현 가능성
- 썸네일/미리보기/임시 파일 동시 삭제

제품 측 확인 포인트:

- 보관 기간이 사용자 고지와 일치하는가
- 재제출 시 이전 원본을 재사용하지 않는가
- 삭제 실패가 사용자 권리 침해로 이어지지 않게 운영 대응이 있는가

### D-003 관리자 권한/감사 로그 설계

파일 제안: `docs/admin-permission-audit-design.md`

작성할 내용:

- 관리자 역할과 권한 매트릭스
- 인증자료 열람 사유 강제 방식
- 감사 로그 이벤트 종류
- 감사 로그 변경 방지 또는 append-only 설계
- 관리자 내부 메모와 사용자 표시 사유 코드 분리
- 위험 작업 승인 또는 이중 확인 기준

제품 측 확인 포인트:

- 운영 정책의 역할 구분과 권한 모델이 일치하는가
- 열람, 승인, 반려, 권한 변경이 빠짐없이 로그로 남는가
- 내부 메모가 사용자 표시 문구와 섞이지 않는가

### D-004 Capacitor 앱 구현 리스크 문서

파일 제안: `docs/capacitor-app-security-risk.md`

작성할 내용:

- 카메라/갤러리/파일 업로드 플러그인 사용 경계
- 인증자료가 WebView localStorage, IndexedDB, 캐시에 남지 않게 하는 기준
- 세션 토큰, 푸시 토큰, 인증자료 임시 파일 저장소 분리
- 네트워크 실패와 중복 액션 방지
- 딥링크, 권한 프롬프트, 앱 심사 리스크
- `src/platform/` 어댑터 뒤로 네이티브 호출을 숨기는 구조

제품 측 확인 포인트:

- 사용자 화면의 업로드/재시도/철회 UX와 구현 제약이 맞는가
- 로컬 잔존 방지 기준이 개인정보 정책과 충돌하지 않는가
- 중복 수락/거절/심사 액션 방지 기준이 추천 정책과 맞는가

### D-005 추천/매칭 API 경계 문서

파일 제안: `docs/matching-recommendation-api-boundary.md`

작성할 내용:

- 후보군 생성과 AI 추천 이유 생성을 분리하는 API 경계
- 후보 제외 조건의 실행 순서
- 추천 카드 액션의 idempotency key
- `reason`, `caution`, `confidence`, `source_version` 구조
- AI에 전달 가능한 feature와 금지 feature
- 운영자 큐레이션 개입 지점

제품 측 확인 포인트:

- AI가 최종 판단자가 아니라 설명/보조 역할로 제한되는가
- 차별적 우열 표현 금지 기준이 출력 구조에 반영되는가
- 셀렉트 추천 opt-in/철회가 후보군 생성에 즉시 반영되는가

## 5. 병렬 작업 순서

### 1차 병렬 작업

제품/운영 담당:

1. `docs/verification-evidence-lifecycle-spec.md`
2. `docs/exposure-policy.md`
3. `docs/matching-policy.md`

개발/구현/보안 담당:

1. `docs/api-db-design-draft.md`
2. `docs/verification-storage-deletion-design.md`
3. `docs/admin-permission-audit-design.md`

### 2차 병렬 작업

제품/운영 담당:

1. `docs/user-flow-screen-spec.md`
2. `docs/admin-review-operations-policy.md`

개발/구현/보안 담당:

1. `docs/capacitor-app-security-risk.md`
2. `docs/matching-recommendation-api-boundary.md`

## 6. 완료 조건

각 문서는 다음 조건을 만족해야 완료로 본다.

- 기존 문서와 충돌하는 정책이 있으면 본문에 바로 반영하지 않고 쟁점으로 분리한다.
- 공개 저장소에 실제 개인정보, 인증자료, 내부 키, 토큰, 로그 원문을 넣지 않는다.
- 사용자에게 보이는 상태와 운영자 내부 상태를 분리한다.
- AI 추천에는 인증자료 원본, 민감 식별정보, 관리자 내부 메모가 들어가지 않는다.
- 일반 회원 opt-in, 셀렉트 목록 비공개, 원본 장기 보관 금지 원칙을 깨지 않는다.
- 구현 문서는 제품 정책의 예외를 임의로 만들지 않는다.
- 제품 문서는 구현 불가능한 보안/삭제 약속을 임의로 확정하지 않는다.

## 7. 크로스 체크 체크리스트

### 정책 일관성

- 일반 회원은 opt-in 전 셀렉트/프리미엄 회원에게 추천 후보로 들어가지 않는가
- 셀렉트/프리미엄 회원 목록은 일반 회원에게 직접 공개되지 않는가
- 인기 일반 회원은 셀렉트/프리미엄 회원으로 오해되지 않게 표현되는가
- 등급, 검증, 추천 이유에 우열 표현이 없는가

### 개인정보/보안

- 인증자료 원본, OCR 원문, 고유식별번호 원문이 DB에 저장되지 않는가
- 원본 보관 기간과 삭제 대기열 설계가 사용자 고지와 일치하는가
- 관리자 열람, 승인, 반려, 권한 변경이 감사 로그로 남는가
- Capacitor WebView와 로컬 캐시에 민감자료가 남지 않게 설계되었는가

### API/DB 구현 가능성

- 회원 등급, 계정 상태, 검증 상태, 노출 동의가 별도 필드/테이블로 분리되는가
- 추천 후보군 생성에서 opt-in, 차단, 신고, 제한 상태가 강제되는가
- 추천 카드 액션과 관리자 심사 액션은 중복 처리되지 않는가
- 재검증, 만료, 삭제 재시도 같은 배치 작업이 문서화되어 있는가

### UX/운영

- 사용자 화면에서 opt-in, 철회, 검증 신청, 재제출, 반려 사유가 명확한가
- 운영자 화면에서 열람 사유 입력 없이 인증자료를 볼 수 없게 되어 있는가
- 반려 사유 코드와 내부 메모가 분리되는가
- 신고/차단/제재가 추천 노출과 검증 상태에 어떻게 반영되는지 명확한가

## 8. 크로스 체크 결과 기록 방식

양쪽 작업이 끝나면 `docs/collaboration-log.md`에 다음 형식으로 기록한다.

```md
## 2026-05-21 문서 크로스 체크

### 확인한 제품/운영 문서

- ...

### 확인한 개발/구현/보안 문서

- ...

### 충돌 없음

- ...

### 수정 필요

- ...

### 다음 액션

- ...
```
