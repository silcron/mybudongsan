# Chapter 5: My 부동산

**모바일프로그래밍 수업 · Flutter 기반 아파트 부동산 정보 앱**

Google Maps 지도에서 주변 아파트를 검색하고, Cloud Firestore에 저장된 아파트 정보와 거래 내역을 조회하는 Chapter 5 실습 프로젝트입니다. Chapter 4 성격 검사 앱에 이어 지도 UI, 위치 기반 데이터 조회, 검색 조건, 화면 전환과 설정 저장을 학습했습니다.

- [모바일프로그래밍 수업 프로젝트 목록](https://github.com/silcron/mobile-programming)
- [Chapter 4: Personality Test](https://github.com/silcron/personalitytest)

## 주요 구현 내용

| 구성 | 코드에서 확인할 내용 |
|---|---|
| 지도 화면 | Google Maps 초기 위치, 아파트 이미지 마커, 마커 정보창, 지도·목록 탭 전환 |
| 위치 기반 검색 | 현재 지도의 보이는 영역에서 중심 좌표를 계산하고, GeoFlutterFire로 반경 1km의 Firestore 문서 검색 |
| 검색 조건 | 동 수·세대 수·주차 조건을 드롭다운으로 선택하고 지도 마커 표시 조건에 적용 |
| 아파트 상세 | 이름·주소·동 수·세대 수·주차 대수·면적별 세대 수 표시 |
| 거래 내역 | FirestoreListView로 거래일·층·가격·면적을 표시하고 시작 연도 슬라이더로 조회 조건 조정 |
| 찜 화면 | Firestore 문서 저장·삭제와 저장한 아파트의 상세 화면 이동 코드 구성 |
| 지도 설정 | terrain·satellite·hybrid 선택값을 SharedPreferences에 저장하고 지도에 반영 |
| 오류 수집 | Firebase 초기화 후 Flutter 프레임워크 및 비동기 오류를 Crashlytics에 연결 |

## 사용자 흐름

`지도에서 위치 선택 → 이 위치로 검색하기 → 마커 또는 목록 선택 → 아파트 상세·거래 내역 확인 → 찜 등록 및 조회`

검색 아이콘에서 조건을 변경하고, 사이드 메뉴에서 찜 목록과 지도 설정으로 이동합니다.

## 코드 구조

```text
lib/
  main.dart                      앱 시작, Firebase·Crashlytics 초기화
  firebase_options.dart          플랫폼별 Firebase 설정 예제
  intro/intro_page.dart          연결 상태 확인과 안내 화면
  map/map_page.dart              지도·목록, 주변 검색, 마커 필터
  map/map_filter.dart            검색 조건 모델
  map/map_filter_dialog.dart     조건 선택 UI
  map/apt_page.dart              아파트 상세, 거래 내역, 찜 처리
  myFavorite/my_favorite_page.dart  찜 목록과 상세 이동
  settings/setting_page.dart     지도 설정 저장과 화면 이동
  geoFire/                       위치·지오해시 검색 관련 코드
res/
  images/apartment.png           아파트 마커 이미지
  json/apt_test_data.json        실습용 데이터 자료
```

## 구현 과정에서 다룬 학습 내용

1. **지도와 Flutter 상태 연결:** GoogleMapController, CameraPosition, Marker 및 지도·목록 전환을 구성했습니다.
2. **클라우드 데이터 조회:** 지도의 중심점과 반경을 검색 조건으로 만들고 Firestore의 위치 데이터와 연결했습니다.
3. **조건에 따른 화면 구성:** 필터 모델과 선택 대화상자를 분리하고 반환된 조건을 마커 표시 로직에 전달했습니다.
4. **상세 화면과 쿼리:** 선택한 아파트 정보를 다음 화면으로 전달하고 거래 연도 조건으로 목록을 조회했습니다.
5. **저장과 설정:** Firestore 문서 쓰기·삭제, SharedPreferences 설정 저장, 오류 수집을 적용했습니다.

## 실행 준비

공개본에는 실제 API 키를 포함하지 않았습니다. 원본 앱의 기능 코드를 확인할 수 있도록 설정 위치는 유지하되 Firebase 키는 예제 문자열로 바꾸고, Android 지도 키는 Git에서 제외되는 로컬 설정에서 읽도록 구성했습니다.

1. Flutter SDK와 Android 개발 환경을 준비합니다. Dart SDK 조건은 `pubspec.yaml`의 `^3.6.1`을 따릅니다.
2. 의존성을 설치합니다.

   ```sh
   flutter pub get
   ```

3. Firebase CLI와 FlutterFire CLI를 준비하고 **본인이 관리하는 Firebase 프로젝트**로 설정 파일을 다시 생성합니다.

   ```sh
   flutterfire configure --project=YOUR_FIREBASE_PROJECT_ID --platforms=android,ios
   ```

4. Google Cloud에서 Maps SDK for Android용 키를 준비하고 패키지·서명 및 API 사용 제한을 적용합니다. `android/local.properties`에 아래 항목을 추가합니다. 기존 SDK 경로 설정은 유지합니다.

   ```properties
   MAPS_API_KEY=YOUR_GOOGLE_MAPS_API_KEY
   ```

   `android/local.properties.example`을 참고할 수 있습니다. 실제 키가 들어간 로컬 파일은 커밋하지 않습니다.

5. Cloud Firestore에 실습 데이터를 준비하고 필요한 접근 규칙·색인을 설정합니다.
6. 에뮬레이터 또는 Android 기기를 연결해 실행합니다.

   ```sh
   flutter run
   ```

현재 지도 키 설정 예시는 Android 기준입니다. iOS에서 지도를 실행하려면 Maps SDK for iOS 설정과 네이티브 초기화도 별도로 준비해야 합니다.

### Firestore 데이터 구조 참고

- `cities`: 지도 검색 대상. `name`, `address`, `position.geohash`, `position.geopoint`, `ALL_DONG_CO`, `ALL_HSHLD_CO`, `CNT_PA` 등의 필드를 사용합니다. `geopoint`는 Firestore GeoPoint 자료형입니다.
- `wydmu17me`: 상세 화면의 거래 내역 조회 대상. `deal_ymd`, `floor`, `obj_amt`, `bldg_area`를 사용합니다.
- `rollcake/favorite`: 찜 저장용 실습 문서입니다. 현재 코드는 공유 문서 한 개를 사용하며, 사용자별 로그인·찜 분리 구조는 포함하지 않습니다.

`res/json/apt_test_data.json`은 자료 파일이며 현재 지도 검색 화면은 Firestore를 조회합니다. 앱 실행 시 자동으로 해당 JSON을 가져오거나 업로드하는 흐름은 아닙니다.

## 업로드 기록과 확인 범위

| 날짜 | 내용 |
|---|---|
| 2026.11.13 | 제공된 `mybudongsan_final_code` 소스를 Chapter 5 저장소로 정리하고, 기능·구조·실행 준비를 문서화했습니다. 실제 API 키와 빌드·캐시·개인 개발 환경 파일을 공개 대상에서 제외했습니다. |

기능 설명은 업로드 소스 검토를 기준으로 작성했습니다. 이번 업로드 과정에서 기기 실행이나 APK 빌드 성공을 새로 검증한 것은 아닙니다. 시작 화면은 현재 `MapPage`이며, `IntroPage`는 별도 화면 코드로 포함됩니다. 네트워크 연결 확인이나 찜 상태 처리 등은 실습 코드의 후속 개선 대상입니다.
