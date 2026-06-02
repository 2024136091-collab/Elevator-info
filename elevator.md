---
marp: true
theme: default
paginate: true
backgroundColor: '#1a1a2e'
color: '#eaeaea'
style: |
  h1 { color: #e94560; }
  h2 { color: #0f3460; background: #e94560; padding: 4px 12px; border-radius: 4px; }
  table { font-size: 0.85em; }
  table td, table th { color: #eaeaea !important; border-color: #0f3460 !important; }
  table tr { background-color: rgba(255,255,255,0.05) !important; }
  table tr:nth-child(2n) { background-color: rgba(255,255,255,0.1) !important; }
  code { background: #0f3460; color: #eaeaea; }
  blockquote { color: #eaeaea; border-left-color: #e94560; }
  li { color: #eaeaea; }
---

# 승강기 정보검색 앱
### Flutter 앱 프로그래밍 프로젝트

**신구대학교 컴퓨터소프트웨어학과**
앱프로그래밍응용 | 12주차 발표

---

## 한 줄 소개

> 공공데이터를 활용해 승강기 설치·점검·위치 정보를
> **한 곳에서 빠르게 조회하는 Flutter 앱**

- 플랫폼: Flutter (Android / iOS)
- 데이터: 행정안전부 승강기 안전관리 공공 API
- 주요 기능: 검색 · 점검 이력 · 지도 위치 조회

---

## 왜 이 주제를 골랐나

| 문제 | 설명 |
|---|---|
| 정보 분산 | 승강기 정보가 여러 사이트에 흩어져 있음 |
| 모바일 미지원 | 기존 서비스는 PC 위주 |
| 검색 불편 | 건물명·호기 번호로 빠른 검색 불가 |
| **Flutter 앱** ✅ | 공공 API + 모바일 UI로 한 번에 해결 |

---

## 핵심 기능

| 기능 | 내용 |
|---|---|
| 승강기 검색 | 건물명·주소·승강기 번호로 검색 |
| 점검 이력 조회 | 최근 점검 날짜·결과·담당자 확인 |
| 위치 조회 | 지도에서 승강기 설치 위치 확인 |
| 상세 정보 | 제조사·설치연도·최대 정원 등 표시 |
| 즐겨찾기 | 자주 사용하는 승강기 로컬 저장 |

---

## 아키텍처 — 4레이어

```
Presentation  →  화면 5개 (Flutter 위젯)
Application   →  Riverpod Notifier
Domain        →  ElevatorService · SearchService
Data          →  API Repository (공공데이터) · LocalRepository (Hive)
```

> 화면은 Notifier만 바라본다.
> Domain은 Flutter를 모른다 → 단위 테스트 가능.

---

## Flutter 구현 화면

| 화면 | 주요 기능 |
|---|---|
| 홈 | 검색창, 최근 조회 목록 |
| 검색 결과 | 승강기 목록, 필터링 |
| 상세 정보 | 승강기 스펙, 점검 이력 |
| 지도 | 설치 위치 마커 표시 |
| 즐겨찾기 | 로컬 저장 목록 관리 |

---

## 기술 선택 근거

| 기술 | 선택 이유 |
|---|---|
| Flutter | 단일 코드베이스로 Android·iOS 동시 지원 |
| Riverpod | 상태 관리, 비동기 API 처리 용이 |
| Hive | 즐겨찾기 오프라인 저장 |
| Google Maps | 위치 조회 지도 표시 |
| Dio | HTTP 클라이언트, 공공 API 연동 |

---

## 현재까지 완료한 것

- ✅ 요구사항 · WBS · 일정 문서
- ✅ 4레이어 아키텍처 설계
- ✅ 공공데이터 API 연동 테스트
- ✅ 검색 화면 UI 구현
- ✅ 승강기 목록 · 상세 정보 화면 구현
- ⬜ 지도 위치 조회 구현 중
- ⬜ 즐겨찾기 기능 구현 예정

---

## 다음 주 (13주차) 목표

1. 지도 위치 조회 완성
2. 즐겨찾기 기능 구현
3. UI 디자인 다듬기
4. 단위 테스트 작성

---

## Q&A

> "AI가 그렇게 했어요"는 답이 되지 않습니다.
> 모든 설계 결정에 이유를 말할 수 있습니다.

---

# 감사합니다

**신구대학교 컴퓨터소프트웨어학과**

학번: 2024136091
이름: 김재호
