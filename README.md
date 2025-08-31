1. 문제 정의 & 목표 설정

왜 이 프로젝트를 하는가?

기존 Coinglass 같은 플랫폼은 CEX(중앙화 거래소) 데이터 기반임.

온체인 선물거래소(Hyperliquid)의 모든 거래 데이터를 오픈소스로 접근 가능 → 투명한 분석 도구 만들 수 있음.

최종 목표

Hyperliquid BTC Perpetual 거래의 모든 유저 포지션을 수집 → 청산 가격대별 시각화.

상위 트레이더들의 포지션을 리더보드 형태로 제공.

MVP 범위

Step1: BTC 선물 거래 데이터 수집 & DB저장

Step2: 기본 청산 히트맵 시각화

Step3: 리더보드 페이지

2. 요구사항 정의
(1) 기능 요구사항

데이터 수집

WebSocket → Hyperliquid 거래 체결 이벤트 실시간 구독

buyer/seller 주소, 포지션 사이즈, 가격 기록

데이터 가공

각 지갑별 현재 포지션 유지 (롱/숏, 규모, 평균 진입가, 청산가 추정치)

유저별 포지션 총합 집계

시각화

청산 히트맵 (가격대별 청산 물량 예상)

리더보드 (상위 포지션 보유 지갑 순위)

웹페이지

실시간 차트 업데이트

반응형 UI (모바일/PC 지원)

(2) 비기능 요구사항

성능: 초당 수십~수백 건 거래처리 가능해야 함.

확장성: 나중에 ETH, SOL 등 다른 코인도 쉽게 확장 가능.

보안: DB 유저 데이터는 지갑주소 외 민감정보 없음 → 보안 부담 적음.

배포: AWS/클라우드 서버에 웹서비스 배포.

3. 아키텍처 설계

데이터 수집 레이어

Rust/Node.js → WebSocket → DB (PostgreSQL or MySQL)

DB 모델링

trades (체결 데이터 기록용)

positions (각 지갑의 최신 포지션 상태)

leaderboard (집계 결과 캐시)

백엔드 API

REST/GraphQL → 프론트에 데이터 제공

프론트엔드

React (차트 라이브러리: TradingView Charting Library / Recharts / D3.js)

차트: 청산 히트맵 / 포지션 리더보드

4. 프로토타입 & UX 기획

와이어프레임 작성

홈화면 → BTC 청산 히트맵

탭1: 차트(청산 히트맵)

탭2: 리더보드 (상위 포지션 보유 유저)

사용자 플로우

접속 → 실시간 차트 확인 → 특정 가격대 클릭 → 관련 유저 주소 확인

5. 기술 스택 결정

데이터 수집: Rust (성능/안정성) or Node.js (빠른 개발)

DB: PostgreSQL (대용량 시계열 데이터 적합)

백엔드: Express.js / Actix-web(Rust)

프론트: React + Recharts/Chart.js/TradingView

배포: AWS EC2 + Docker + Nginx

6. 로드맵 (Milestone)

1주차: Hyperliquid WebSocket 연결 → BTC 체결 데이터 저장 (trades 테이블)

2주차: DB 기반 지갑별 포지션 계산 로직 구현 (positions 테이블)

3주차: 청산가 추정 알고리즘 구현 → 가격대별 청산 규모 집계

4주차: 프론트엔드 기본 차트 구현 (히트맵)

5주차: 리더보드 페이지 추가

6주차: 배포 & 테스트

7. 리스크 & 고려사항

Hyperliquid 데이터 구조 변경 → WebSocket 스키마 모니터링 필요

데이터량 폭증 → 장기 보관 정책 (압축, 파티셔닝)

실시간 처리 속도 → Redis 캐시 고려

법적 이슈 → 단순 공개데이터 활용이므로 개인정보 문제 없음
