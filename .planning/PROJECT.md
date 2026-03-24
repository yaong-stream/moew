# moew

## What This Is

Twitch와 같은 실시간 라이브 스트리밍 플랫폼. 스트리머가 OBS로 방송을 송출하면 WebRTC 기반 SFU를 통해 시청자에게 저지연으로 전달한다. 시청자는 브라우저에서 실시간 채팅과 함께 방송을 시청할 수 있다.

## Core Value

스트리머의 방송이 시청자에게 WebRTC로 저지연 전달되고, 실시간 채팅으로 소통할 수 있어야 한다.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] 스트리머가 OBS로 미디어 서버에 스트림을 송출할 수 있다
- [ ] SFU가 수신한 스트림을 WebRTC로 시청자에게 저지연 전달한다
- [ ] 1만명 동시 시청자를 처리할 수 있는 SFU 아키텍처
- [ ] 시청자가 브라우저에서 라이브 스트림을 시청할 수 있다
- [ ] 이메일/비밀번호 기반 회원가입 및 로그인
- [ ] 인증 보안 (JWT, 비밀번호 해싱, 세션 관리)
- [ ] 실시간 채팅 (WebSocket 기반)
- [ ] 스트리머 팔로우 기능
- [ ] 채널 구독 기능
- [ ] 라이브 채널 목록 / 탐색
- [ ] Production-ready 보안 (HTTPS, CORS, rate limiting, input validation)

### Out of Scope

- 후원/도네이션 — v1 이후, 결제 시스템 복잡도
- 소셜 로그인 (OAuth) — v1은 이메일/비밀번호로 충분
- 브라우저 직접 송출 — v1은 OBS 전용
- VOD/다시보기 — 라이브 스트리밍에 집중
- 모바일 앱 — 웹 우선
- 이모티콘/뱃지 시스템 — v1 이후
- 클립/하이라이트 — v1 이후

## Context

- Bun 기반 모노레포: Next.js (web) + NestJS (api)
- Biome 린터/포매터, TailwindCSS, `@/*` path alias
- SFU 직접 구현 (mediasoup 등 라이브러리 활용 예상)
- OBS → RTMP/WHIP 인제스트 → SFU → WebRTC 시청자 전달 구조
- 실시간 채팅은 WebSocket 기반
- 1만명 동시 시청자 목표 — SFU 스케일링 전략 필요

## Constraints

- **Tech stack**: Bun + Next.js + NestJS 모노레포 (이미 구축됨)
- **Streaming**: WebRTC 기반 저지연 필수 — HLS/DASH 폴백 아님
- **Scale**: 1만명 동시 시청자 처리
- **Security**: Production-ready 보안 수준 (인증, 입력 검증, rate limiting 등)
- **SFU**: 직접 구현 (관리형 서비스 아님)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| WebRTC over HLS/DASH | 저지연이 핵심 가치 | — Pending |
| SFU 직접 구현 | 제어권 확보, 커스터마이징 가능 | — Pending |
| OBS 전용 송출 (v1) | 브라우저 송출 복잡도 배제, v1 집중 | — Pending |
| 이메일/비밀번호 인증 (v1) | OAuth 없이 단순하게 시작 | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd:transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-03-24 after initialization*
