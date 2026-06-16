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
  code { background: #0f3460; color: #eaeaea; font-size: 0.8em; }
  blockquote { color: #eaeaea; border-left-color: #e94560; }
  li { color: #eaeaea; }
---

# 승강기 정보검색 앱
### Flutter 앱 프로그래밍 프로젝트

**신구대학교 컴퓨터소프트웨어학과**
앱프로그래밍응용 | 12주차 발표

---

## 목차

1. 비전 · 기획 배경
2. WBS · 활용 방안
3. 핵심 기능 · 아키텍처
4. 구현 상세 (API · 데이터 모델 · 상태 관리 · AI 검색)
5. 개발환경 · 빌드/배포 · ADR
6. 진행 현황 · 시연 데모 · Q&A

---

## 비전

> **승강기 안전 정보를 누구나 손 안에서 빠르게 확인할 수 있는 세상**

대한민국 승강기 **80만 대 이상** — 하지만 정보는 PC 전용 웹에만 존재합니다.
공공 API + Flutter로 이 정보를 모바일·웹에서 누구든 즉시 조회할 수 있게 합니다.

---

## 왜 이 주제를 골랐나

| 문제 | 설명 |
|---|---|
| 정보 분산 | 승강기 정보가 여러 사이트에 흩어져 있음 |
| 모바일 미지원 | 기존 서비스는 PC 위주 |
| 검색 불편 | 건물명·호기 번호로 빠른 검색 불가 |
| **Flutter 앱** ✅ | 공공 API + 모바일 UI로 한 번에 해결 |

---

## WBS — 개발 일정

| 주차 | 작업 | 상태 |
|---|---|---|
| 9주차 | 요구사항 분석 · 기획 | ✅ |
| 10주차 | 아키텍처 설계 · 환경 구성 | ✅ |
| 11주차 | 핵심 기능 구현 (검색·필터·즐겨찾기) | ✅ |
| 12주차 | 추가 기능 · 발표 준비 · 문서화 | ✅ |
| 13주차 | 실제 API 연동 · Android·iOS 빌드 | 🔄 |

---

## 활용 방안 및 기대 효과

| 대상 | 활용 방안 |
|---|---|
| 건물 관리자 | 담당 건물 승강기 점검 이력 즉시 확인 |
| 입주민 | 아파트 승강기 운행 상태 실시간 조회 |
| 안전 점검원 | 현장에서 모바일로 승강기 정보 조회 |
| 민원 담당자 | 민원 접수 시 승강기 번호로 빠른 검색 |

> 향후 실제 API 연동 시 **80만 대** 전체 승강기 정보 실시간 제공 가능

---

## 핵심 기능

| 기능 | 내용 |
|---|---|
| 승강기 검색 | 건물명·주소·승강기 번호로 검색 |
| **스마트 자연어 검색** | 키워드 규칙 기반으로 문장형 질의 해석·검색 |
| 지역 필터 | 서울·경기·강원 등 권역별 필터링 |
| 건물 유형 필터 | 학교·병원·오피스·상업시설·아파트 + '업체'(아파트 제외 비주거 건물만) 필터 |
| 건물별 그룹핑 | 검색 결과를 건물 단위로 묶어 표시 |
| 점검 이력 조회 | 최근 점검 날짜·결과·담당자 확인 |
| **위치 지도 조회** | OpenStreetMap 기반 건물 위치 지도 표시 |
| 즐겨찾기 | 자주 사용하는 승강기 로컬 저장 |

---

## 아키텍처 — 4레이어

| 레이어 | 구성 요소 | 역할 |
|---|---|---|
| Presentation | Flutter 위젯 (화면 5개) | UI 표시, 사용자 입력 |
| Application | Riverpod Notifier | 상태 관리, 비동기 처리 |
| Domain | ElevatorService | 검색·필터 비즈니스 로직 |
| Data | ApiRepository · LocalRepository | API 호출 · 즐겨찾기 저장 |

> Presentation → Application → Domain → Data (단방향 의존)

---

## 프로젝트 구조

```
lib/
├── domain/
│   ├── models/elevator.dart            Elevator · InspectionRecord
│   └── services/elevator_service.dart  검색·필터·AI 분석 로직
├── data/repositories/
│   ├── api_repository.dart             더미 데이터 (API 준비)
│   └── local_repository.dart           즐겨찾기 저장
├── application/providers/
│   ├── elevator_providers.dart         SearchNotifier (AI 포함)
│   └── favorites_providers.dart        FavoritesNotifier
└── presentation/screens/
    ├── home_screen.dart                홈 (AI 검색 진입 버튼)
    ├── ai_search_screen.dart           스마트 자연어 검색 화면
    ├── search_result_screen.dart       검색 결과 (건물별 그룹)
    ├── detail_screen.dart              승강기 상세 정보
    └── favorites_screen.dart           즐겨찾기 목록
```

---

## 국가승강기정보센터 API

번호 형식: `XXXX-XXX` (예: 승강기 `2091-142`, 에스컬레이터 `3819-208`, 수직형리프트 `3902-291`)

| 지역 코드 | 해당 지역 |
|---|---|
| 0 · 18 · 19 | 서울 |
| 2 · 38 · 39 | 경기·인천 |
| 4 · 48 · 49 | 강원 |
| 5 · 58 · 59 | 충청권 |
| 6 · 68 · 69 | 경북권 |
| 7 · 78 · 79 | 호남권 |
| 8 · 88 · 89 | 영남권 |
| 9 · 98 · 99 | 제주 |

---

## 데이터 모델

```dart
class Elevator {
  final String id;               // 국승정 번호 (XXXX-XXX)
  final String buildingName;
  final String buildingCategory; // 학교·아파트·오피스·상업시설·병원 (업체=아파트 제외 필터)
  final String address;
  final String type;             // 승객용·에스컬레이터·수직형리프트
  final int    installYear;
  final String manufacturer;
  final int    capacity;         // 에스컬레이터는 0
  final String lastInspectedAt;
  final bool   isOperating;
  final double? lat;             // 위도 (OpenStreetMap 마커)
  final double? lng;             // 경도

  String get region { /* 번호 앞자리로 지역 자동 판별 */ }
}
```

---

## Riverpod 상태 관리

```dart
// Application 레이어 — SearchNotifier
class SearchNotifier
    extends StateNotifier<AsyncValue<List<Elevator>>> {

  Future<void> search(String query) async {
    state = const AsyncValue.loading();
    state = await AsyncValue.guard(
      () => _repo.search(query), // Data 레이어 호출
    );
  }
}

final searchProvider =
    StateNotifierProvider<SearchNotifier,
        AsyncValue<List<Elevator>>>((ref) => SearchNotifier(...));
```

> `AsyncValue` — 로딩·에러·데이터 상태를 한 타입으로 처리

---

## 스마트 자연어 검색 (규칙 기반)

```dart
// Domain — ElevatorService.aiSearch() (규칙 기반 자연어 해석)
({List<Elevator> results, String description}) aiSearch(
    List<Elevator> elevators, String query) {
  final q = query.toLowerCase();

  if (q.contains('오래된') || q.contains('노후')) {
    final sorted = [...elevators]
      ..sort((a, b) => a.installYear.compareTo(b.installYear));
    return (results: sorted.take(5).toList(),
            description: '설치연도가 오래된 순으로 표시합니다.');
  }
  if (q.contains('장애인') || q.contains('휠체어'))
    return (results: elevators.where(
              (e) => e.type.contains('장애인')).toList(),
            description: '장애인용 승강기만 표시합니다.');
  // 점검·병원·학교·화물·전망 등 키워드 추가 처리
}
```

> 키워드 미매칭 시 일반 검색으로 fallback / UI: 보라색 AI 테마, 예시 질문 칩, 점 애니메이션

---

## 구현 화면

| 화면 | 주요 기능 |
|---|---|
| 홈 | 검색창, 스마트 검색 진입 버튼, 최근 조회 |
| **스마트 검색** | 문장형 입력, 예시 칩, 키워드 분석 결과 배너 |
| 검색 결과 | 건물별 그룹핑, 지역·유형·종류 3단 필터 |
| 상세 정보 | 기본정보·제원·점검이력·위치 지도, 즐겨찾기 토글, 국가승강기정보센터 외부 링크 |
| 즐겨찾기 | 저장된 목록, 해제 기능 |

---

## 개발환경 설정

| 항목 | 내용 |
|---|---|
| Flutter SDK | 3.41.9 stable |
| 언어 | Dart (Flutter 내장) |
| 편집기 | VS Code |
| 버전 관리 | Git + GitHub |
| 빌드 대상 | Web (Chrome) |

```bash
flutter config --enable-web   # Web 지원 활성화
flutter pub get               # 패키지 설치
flutter run -d web-server     # 디버그 실행
flutter analyze               # 정적 분석 (error 0건)
```

---

## 빌드 및 배포

```
소스 코드 → flutter build web --release → build/web/ → GitHub Pages
```

| 단계 | 명령 / 방법 |
|---|---|
| Release 빌드 | `flutter build web --release` |
| 로컬 확인 | `python -m http.server 8081` |
| 발표자료 변환 | `npx @marp-team/marp-cli elevator.md -o index.html` |
| 배포 | `git push` → GitHub Pages 자동 반영 |

**배포 URL**: [https://2024136091-collab.github.io](https://2024136091-collab.github.io)
**앱 실제 데모**: [https://2024136091-collab.github.io/app/](https://2024136091-collab.github.io/app/)

---

## ADR — 주요 설계 결정

| ADR | 결정 | 근거 |
|---|---|---|
| 001 | **4레이어 아키텍처** 채택 | API 교체 시 Data 레이어만 수정, 나머지 불변 |
| 002 | **Riverpod** 상태 관리 선택 | AsyncValue로 로딩·에러·데이터 3상태 일원화 |
| 003 | **더미 데이터 우선** 개발 | API 인증키 없이 전체 기능 개발·테스트 가능 |

> 전체 ADR: `docs/adr/` 폴더에 기록 (GitHub 저장소)

---

## 구현 시행착오

| 문제 | 원인 | 해결 |
|---|---|---|
| Marp 렌더링 오류 | 코드 블록 내 예상치 못한 파싱 | 코드 블록 → 표(table)로 교체 |
| `_service` 접근 불가 | Presentation에서 private 메서드 호출 시도 | `SearchNotifier`에 `groupByBuilding()` 메서드 노출 |
| `filterByRegionAndType` 오류 | 카테고리 필터 추가 후 메서드 시그니처 불일치 | `filterByAll(region, type, category)`로 통합 |
| 중복 폴더 문제 | `lib/screens/`와 `lib/presentation/screens/` 공존 | 구버전 `lib/screens/` 삭제, 4레이어 구조로 일원화 |
| `latlong2` URI 오류 | 라이브러리 파일명이 `latlong2.dart`가 아닌 `latlong.dart` | 임포트를 `package:latlong2/latlong.dart`로 수정 |

---

## 코드 품질 및 테스트

**정적 분석**
```bash
flutter analyze   # error 0건
```

**단위 테스트** (`test/widget_test.dart`)
```dart
testWidgets('홈 화면 렌더링 테스트', (tester) async {
  await tester.pumpWidget(ProviderScope(child: ElevatorApp()));
  expect(find.text('승강기 정보검색'), findsOneWidget);
  expect(find.byIcon(Icons.search), findsOneWidget);
});
```

**GitHub 커밋**: 기능별 단계적 커밋으로 변경 이력 관리

---

## 기술 선택 근거

| 기술 | 선택 이유 |
|---|---|
| Flutter | Android·iOS·Web 단일 코드베이스, Web 빌드 완료 |
| Riverpod | `AsyncValue`로 로딩·에러·데이터 상태 일원화, Provider 전역 오염 없음 |
| LocalRepository | 즐겨찾기처럼 단순 구조는 별도 DB 없이 메모리 저장으로 충분 |
| flutter_map | API 키 없이 OpenStreetMap 타일로 Web·모바일 지도 표시 가능 |
| Dio (예정) | `Interceptor`로 API 키 헤더 자동 주입, 재시도 로직 중앙 관리 |

---

## 현재까지 완료한 것

- ✅ 요구사항 · WBS · 일정 문서 작성
- ✅ 4레이어 아키텍처 설계 및 구현
- ✅ 데이터 모델 (`Elevator`, `InspectionRecord`) 정의
- ✅ 국가승강기정보센터 API 번호 체계 분석 및 적용
- ✅ 홈 · 검색 결과 · 상세 정보 · 즐겨찾기 화면 구현
- ✅ 지역 필터 · 건물 유형 필터 · 승강기 종류 필터 (3단 필터)
- ✅ 건물별 그룹핑 표시
- ✅ **스마트 자연어 검색 기능** 구현 (규칙 기반 키워드 분석)
- 🔄 실제 공공 API 연동 진행 중
- ✅ **위치 지도 조회** 구현 (flutter_map + OpenStreetMap, 핀 마커)

---

## 다음 주 (13주차) 목표

1. 실제 국가승강기정보센터 API 연동 (`ApiRepository` 완성)
2. Dio HTTP 클라이언트 적용 및 API 키 연동
3. Android·iOS 네이티브 빌드 및 테스트
4. UI 디자인 다듬기

---

## 시연 데모 (30초 시나리오)

1. **홈 화면** — "신구대학교" 검색
2. **검색 결과** — 건물별 그룹 확인 · 지역 필터("경기·인천") 적용
3. **상세 정보** — 승강기 번호·점검이력·위치 지도 확인 · 즐겨찾기 등록
4. **스마트 검색** — "오래된 승강기 찾아줘" 입력 → 결과 확인

> "국승정 번호 2018-399로 검색하면 실제 휴먼시아아파트가 나옵니다"
> 지금 바로 체험: [https://2024136091-collab.github.io/app/](https://2024136091-collab.github.io/app/)

---

## Q&A

> ADR 3건 · 아키텍처 · 빌드 과정에 대해 답변 준비되어 있습니다.

---

# 감사합니다

**신구대학교 컴퓨터소프트웨어학과**

학번: 2024136091
이름: 김재호
