# CP CareCloud (Cerebral Palsy Integrated Care Cloud)

AppSheet 기반으로 복지관·IL센터·병원·재활기관을 하나로 묶어 뇌성마비 당사자 돌봄을 통합 관리하는 SaaS 설계 문서입니다. 상계뇌성마비복지관과 상계IL센터 등 여러 기관이 동일한 워크플로로 협업할 수 있도록 구성했습니다.

## 1. 서비스 개요
### 1.1 서비스명
- **CP CareCloud**: 모든 CP 기관에서 공용으로 사용하는 통합 관리 SaaS

### 1.2 해결하려는 문제
- 의료·재활·돌봄 데이터가 기관별로 단절되어 위험 대응이 늦어지는 문제 해결
- 경직·통증 급변, 장비 의존도, 낙상/욕창/흡인 등 스팟 위험, 자기돌봄 역량 편차, 보호자 부담, 다기관 병행 이용 특성을 반영하여 **위험 조기 감지 + 자기돌봄 강화 + 기관 간 협력 자동화** 제공

## 2. 핵심 기능 구조
### 2.1 핵심 모듈
- 스팟 위험지수 AI 모듈 (Spot Risk AI)
- 경직·통증·자세 모니터링
- 휠체어·보조기기·자세유지장비 관리
- 자기돌봄 5단계 루틴 트래커 (AppSheet 자동화)
- 개별 I-Care Plan 관리
- 기관 간 정보 협력
- 보호자 코칭
- 센터 평가·보고서 자동 생성 (PDF/Google Docs)
- AI 상담·분석 챗봇 (CP Assistant)
- 관리자·슈퍼관리자 대시보드

## 3. 사용자 분류
| 구분 | 설명 | 핵심 역할 |
| --- | --- | --- |
| CP 당사자 | 복지관/IL센터 이용자 | 자기돌봄 입력, 위험 점검 |
| 보호자 | 부모·가족 | 건강·장비 상태 보고 |
| 실무자 | 복지관/IL센터 직원 | 케이스 관리, 방문 기록 |
| 치료사 | 물리·작업·언어 치료사 | MAS/NRS/자세 데이터 입력 |
| 센터 관리자 | 복지관/IL센터 팀장급 | 전체 대시보드, 보고서 확인 |
| 기관 관리자 | 지역센터·지자체 | 기관별 성과·위험 통계 |

## 4. AppSheet 기반 SaaS 구조
- 사용자 → AppSheet 앱 → Google Sheets DB → Automation → Gemini 기반 AI 분석 → 보고서·대시보드 → 복지관/IL센터/지자체

## 5. DB 구조 (AppSheet + Google Sheets/BigQuery)
### 5.1 ERD 주요 테이블
- **Users**: UserID(PK), Role(User/Parent/Worker/Therapist/Admin)
- **Clients**: ClientID(PK), GMFCS, CPType, Contact, LinkedGuardianID(FK)
- **SpotRisk**: RecordID(PK), ClientID(FK), BedsoreRisk, FallRisk, AspirationRisk, AcutePain, TotalScore(계산)
- **PainSpasticity**: RecordID, MAS, NRS, BodyRegion
- **Equipment**: EquipID, Type(휠체어/AFO/자세의자), Condition, NextMaintenance
- **SelfCare5**: RecordID, Step1(몸상태), Step2(자세), Step3(스트레칭), Step4(활동관리), Step5(정서·관계)
- **CarePlan**: PlanID, ClientID, Goal, Intervention, 담당자, 평가일
- **SessionLogs**: WorkerID, ClientID, 활동·상담·방문 기록

## 6. 화면 구조 (UI/UX Flow)
### 6.1 홈 화면
- 오늘의 위험 알림, 자기돌봄 5단계 체크, 개인 통증/장비 상태 입력, 다음 일정 표시

### 6.2 직원용 화면
- 대상자 리스트, 스팟 위험지수 Top N, 오늘 방문 예정 대상, I-Care Plan 업데이트, 자동 생성 리포트 다운로드

### 6.3 관리자 대시보드
- GMFCS 단계별 통계, 자기돌봄 단계별 실천율, 장비 상태별 경고, 프로그램 참여율, 센터 전체 위험 Heatmap

### 6.4 지자체/기관 관리자 화면
- 센터별 위험지수 평균, 서비스 이용률, 평가·보고서 자동 생성

## 7. AI 기능 설계 (Gemini 기반)
### 7.1 Spot Risk AI
- **입력**: 욕창·낙상·흡인·통증 수치, 장비 체크리스트, 자기돌봄 실천율, 방문기록 데이터
- **출력**: 위험 점수(0~100), 위험 원인 분석, 개입 우선순위, 추천 서비스(클리닉·자조모임·방문 등)

### 7.2 CP Assistant 챗봇
- 예시 1: “오늘 허리 통증이 심해요.” → 패턴 분석 후 스트레칭·자세 팁 추천
- 예시 2: “내 휠체어 쿠션 꺼짐 체크해줘.” → 장비 교체/점검 알림 자동화

## 8. 자동화 (Automation)
- 스팟 위험 80점 이상 시 직원 Push 알림
- 장비 점검일 도래 시 보호자 SMS
- 자기돌봄 5단계 미완료 시 코칭 메시지 자동 발송
- 센터 보고서 PDF 자동 생성 후 관리자 이메일 발송

## 9. 보안 설계
- 사용자별 Row-Level Security, Google 로그인(OAuth2)
- 보호자-당사자 데이터 연동 제한, 기관별 데이터 분리
- 관리자만 전체 통계 접근, DB Encryption(시트/BigQuery), Audit Log 저장

## 10. 가격 정책 (SaaS Billing)
| 요금제 | 대상 | 제공 기능 |
| --- | --- | --- |
| Free | 개인(CP/보호자) | 자기돌봄 5단계 + 통증 기록 |
| Standard (기관) | 복지관/IL센터 | 케이스 관리 + 위험지수 + 장비관리 |
| Pro | 기관 | AI 위험 분석 + 보고서 자동 생성 |
| Enterprise | 지자체 | 센터 통합 관리 + 평가 리포트 + 통합 데이터 |

## 11. 로드맵
- **1개월**: MVP 화면(AppSheet), DB 기본 스키마, 자기돌봄 5단계 기능, 방문 기록 입력
- **3개월**: 스팟 위험지수 AI, 기관 관리자 대시보드, PDF 리포트 자동 생성
- **6개월**: 멀티 기관/지자체 통합판, 장비 관리 모듈, 역할 기반 로그인
- **12개월**: 예측 분석(Predictive CP Model), 보호자 소진도 분석, 핵심 지표 자동 평가 엔진

## 12. 핵심 가치
- CP 복지관과 IL센터의 데이터를 한눈에 보는 최초의 통합 SaaS
- 사건 발생 이후가 아닌 **사전 예방적 위험 탐지** 중심
- 자기돌봄 5단계를 게이미피케이션으로 적용
- 직원·보호자·당사자가 공동 작업하는 돌봄 클라우드
- 지자체 평가까지 자동 생성되는 행정혁신형 SaaS
