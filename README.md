# Incheon EUM Metaverse

![title](./main.png)

(인천) 시는 인천공항과 개항장·송도, 부평역 등을 대상으로 3D 맵을 제작하며 국내 최고 실내외 측위 기술을 통한 위치 기반 XR 메타버스 플랫폼 구축, 5G 연계 XR 서비스 플랫폼 구축 및 저작도구, 시뮬레이터, 오픈 응용프로그램 인터페이스(API) 개방형 서비스 플랫폼 등 기반을 마련한다. XR 글라스를 통한 다양한 서비스 제공, 서비스 확대, 메타버스 생태계 구축, 써드 파티 육성 및 지원 등도 나선다 [[발췌](https://www.etnews.com/20220620000005)].

인천시를 대상으로한 VPS 기반 서비스 (https://github.com/boosilguy/Metaverse)

## About

인천 지역 5곳 (인천 국제 공항 1터미널, 2터미널, 개항장, 송도, 그리고 부평역)을 대상으로 이미지 기반 학습 모델을 바탕으로 길 찾기, Point Of Interest (POI) 정보 제공 등 제공하는 XR Module을 제공하는 SDK를 개발하는 프로젝트입니다.

뿐만 아니라, 스캔한 지역을 바탕으로 추출한 Geojson을 통해서 지역별 모델 데이터를 추출하여 각 지역 POI 특성 및 서비스 업체에 입 맛에 맞게 편집할 수 있는 맵 저작도구도 동봉되어 배포되는 프로젝트입니다.

우리는 XR SDK와 맵 저작도구를 개발하였고, 이것을 각각 UPM과 Unity App 형태로 배포하여서 각 서비스 업체가 지역별 App과 맵 정보를 쉽게 제작할 수 있도록 기능을 제공하였습니다.

최종적으로 서비스 업체는 우리의 XR SDK와 맵 저작도구를 통해, 아래와 같은 서비스를 만들 수 있게 됩니다.

- 디바이스 (일반 단말기와 XR Glasses)의 카메라에 들어온 Texture를 XR SDK가 입력 받아, 인식된 공간 정보를 디바이스 화면에 렌더링함
    - 맵 저작도구에 업로드된 3D 맵 모델
    - Geojson 추출시, 물고 있었던 Public POI 정보
    - 맵 저작도구에서 등록한 Service POI 정보
- Unity ARFoundation과 연동하여, 실세계 기반 메타버스 체험
    - XR SDK에서 제공하는 XR 네비게이션 서비스
- 맵 저작도구를 통해서 지역 별, 그리고 서비스사 별로 별도의 컨텐츠를 구성
    - Public POI 정보 추가 및 수정 기능
    - Service POI 추가, 수정, 그리고 삭제 기능

### Developer site

협약 및 계약된 업체는 [개발자 사이트](https://xrdeveloper.strato.co.kr/)에서 관리자에게 XR SDK 서비스 토큰과 맵 저작도구 토큰을 발급 받을 수 있습니다. 이에 따라, 지역, 서비스사 별 2 Depth로 개발이 분리되어 작업할 수 있도록 구성되었습니다.

### SDK 사용

UPM의 Scoped Registries 사용 ([Install Guide](https://xrdeveloper.strato.co.kr/documents/sdk/install))하거나, 타르볼 파일로 Local Package 사용.

### SDK를 활용한 서비스 개발 영상

[![IncheonMetaverse-T1](http://img.youtube.com/vi/oLVMMJwwCKI/0.jpg)](http://www.youtube.com/watch?v=oLVMMJwwCKI "T1")

[![IncheonMetaverse-OpenPort](http://img.youtube.com/vi/O1AP2oTbgM4/0.jpg)](http://www.youtube.com/watch?v=O1AP2oTbgM4 "T1")

## SDK configuration

```bash
.
└── XR SDK/
    ├── Editor/
    │   └── Plugins/
    │       ├── Base.Editor.dll
    │       ├── Common.Editor.dll
    │       ├── FFmpegNET.dll
    │       ├── Simulator.Editor.dll
    │       └── Tracker.Editor.dll
    ├── Runtime/
    │   └── Plugins/
    │       ├── GeoAPI.CoordinateSystems.dll
    │       ├── GeoAPI.dll
    │       ├── Google.Protobuf.dll
    │       ├── Proj4Net.dll
    │       ├── SPRF.XR.Base.dll
    │       ├── SPRF.XR.Common.dll
    │       ├── SPRF.XR.DataCenter.dll
    │       ├── SPRF.XR.Map.dll
    │       ├── SPRF.XR.Navigation.dll
    │       ├── SPRF.XR.Network.dll
    │       ├── SPRF.XR.POI.dll
    │       ├── SPRF.XR.Tracker.dll
    │       ├── SPRF.XR.Speech.dll
    │       └── External/
    │           └── ANTracker (NAVER)
    ├── Samples~/
    │   ├── NavWithPOI
    │   ├── ObjectDetection
    │   └── DataTree
    └── Tools/
        └── FFmpeg.exe
```
- Module간의 Dependency를 최소화하기 위해 설계되었으며, Common은 공통적으로 사용할 수 있는 기능 (e.g. Custom Attribute, 외부 오픈소스 등), 그리고 Base는 가장 낮은 레벨의 데이터 (e.g. 맵 관련 클래스의 멤버, POI 데이터 형태), 인터페이스가 존재합니다.
- Setting Module [(`참여`)](./SettingModule/Readme.md)
    - Common에는 위치하며, SDK의 공통적인 내용 및 각 서비스의 설정 요소 (서비스 쪽에서 커스텀하게 새로운 Setting을 추가하였다면)를 조절할 수 있음.
    - SPRFBehaviour (XR SDK 기본 모듈들을 참조할 수 있는 MonoBehaviour), SPRFScriptableObject (XR SDK 기본 모듈들을 참조할 수 있는 ScriptableObject), SPRFObject (XR SDK 기본 모듈들을 참조할 수 있는 클래스)를 상속받거나 사용한 스크립트에서는 새로운 Setting을 Attribute로 주입할 수 있음 (e.g. *[StringSetting("SDK Token", "value")]*).
    - 입사 후 처음으로 작업한 내용. Setting의 Attribute 값이 갱신될 때, Reflection을 통해서 Attribute를 소유한 멤버가 변화를 관찰할 수 있도록 구성하였음 (Dynamic Programming).
- Editor
    - 서비스 측에서 SDK 기능을 편히 사용할 수 있도록 빼놓은 Editor Code와 편의 기능으로 구성되어 있습니다.
    - Simulator.Editor.dll [(`참여`)](./TrackerModule/Readme.md)
        - 시뮬레이터 기능이 없다면 서비스 측에서는 매번 빌드하여 직접 단말기 카메라를 통해, 공간 인식을 성공한 후 테스트를 진행하게 될 것입니다. 우리는 이 점을 고려하여, 단말기 공간인식을 모방한 Simulator 기능을 에디터 코드로 빼 놓았습니다.
        - 프로세스
            1. 상단 메뉴에서 Simulator Window를 Open.
            2. Playmode 진입과 동시에, Tracker는 Texture input을 기다림.
            3. Simulator Window에서 지역 설정 및 Input Type (e.g. 단일 이미지, 이미지 시퀀스, 동영상)에 따른 추가적인 설정 진행. 만약, 동영상의 Display matrix가 0이 아닌 경우 (정방향이 아닌, 수평으로 드러눕는 영상인 경우), 내장된 FFmpeg을 통해, 새롭게 인코딩 가능.
            4. Play 버튼 혹은 공간 인식 버튼을 눌러, Tracker에 Input을 전달.
            동영상의 경우, VideoPlayer Component를 통해 전달.
    - Tracker.Editor.dll [(`참여`)](./TrackerModule/Readme.md)
        - 아무리 Texture Input 기반으로 Tracker가 동작하더라도, 현재 Tracker의 학습 수준이 저급한 상태라면 XR SDK는 공간 인식에 적합한 Texture가 들어올 때까지, 인식 Fail State를 갖게 될 것입니다. 우리는 이 점을 고려하여, Tracker가 정상적으로 공간 인식을 성공했다는 전제를 가정한 Simulation Tracker 기능을 고려하였습니다.
        - Setting Module을 통해 (Setting Editor), Texture input을 off하면 해당 기능을 사용할 수 있도록 구성하였습니다.
        - WASD 이동, 마우스 카메라 이동 등을 지원하며, 층간 이동 및 다른 지역 이동 등을 지원합니다.
        - 프로세스
            1. 상단 메뉴의 Setting Menu에서 Use Texture input based Tracker 기능을 Off.
            2. Playmode 진입과 동시에, User GameObject에 Simulation Tracker Component가 Attached. 테스트 케이스에 맞게, World position, rotation, 층 이동 등을 이행할 수 있음.
- Runtime
    - 실제 서비스 개발을 지원하는 SDK의 구성 및 맵 저작도구에서 사용한 기능이 첨부된 SDK로 구성됩니다.
    - GeoAPI 관련 dll, Proj4Net.dll
        - 네이버 EPSG5179 좌표계를 GPS EPSG4326 좌표계로 변환하여 사용하기 위한 API
        - 새로운 지역 추가 및 기존 지역 수정에 따른 Geojson 원점 및 POI 위치 Sync에 사용.
    - DataCenter.dll
        - 네트워크 통신에 사용되는 모듈. 각 모듈에서 필요한 외부 데이터들을 초기화 단계 혹은 실시간 통신으로 Container에 담아둡니다.
    - Map.dll
        - 맵 저작도구에 지원되는 모듈.
    - Navigation.dll [(`참여`)](./NavigationModule/Readme.md)
        - 네비게이션 서비스를 지원하는 모듈.
        - Geojson의 Node, Link 데이터, 그리고 Joint (인접한 다른 Geojson을 동적 연결)를 바탕으로 A* 알고리즘을 적용하여 동작합니다.
        - 네비게이션은 별도의 State (초기화, 준비, 재탐색, 경유지 도착, 목적지 도착, 에러, 중단)를 갖고 있으며, Tracker의 State에 의존하여 동작합니다.
        - 특수한 이동 수단 (e.g. 계단, 엘레베이터, 에스컬레이터 등)이 존재하는 경우, 해당 수단이 포함하고 있는 Node들은 별도의 Weight 값으로 관리하도록 설계 (모든 길 안내를 엘레베이터로 안내하는 상황 방지).
        - NavigationBehaviour (MonoBehaviour, INavigationEventHandler)를 통해, GameObject가 네비게이션 이벤트를 쉽게 수신하도록 설계. 서비스 측에서는 Navigation을 구성할 수 있는 Configuration에 이벤트들을 Add하여, Navigation 인스턴스를 생성할 때, Configuration을 파라미터로 주입하여 설계할 수 있음. Builder 패턴으로 서비스에서 직관적으로 네비게이션을 구성할 수 있고, 인스턴스화한 직후에도 이벤트 추가 가능토록 구성.
        - DefaultMinimapViewer (NavigationBehaviour)로 기본 미니맵 제공. 각 네비게이션 상태 이벤트에 따라, 미니맵 이벤트 Invoke. MinimapConfiguration ScriptableObject로 기본 제공 기능을 커스터마이징 할 수 있음. 설계된 미니맵 역시, Navigation Configuration에 탑재하여 사용 가능.
        - 부수적인 기능 (모두 Configuration을 통해 설정 가능토록 설계)
            1. SummaryMilestone : 현재 네비게이션에서 목적지까지의 안내된 경로의 종합적인 정보 제공.
            2. PathMilestone : 사용자가 현재 직면한 단위 구간 경로 정보 제공 (e.g. A 지점까지 100m 이동 후 좌회전하세요). TTS와 연동할 수 있는 Flag도 제공.
    - POI.dll [(`참여`)](./POIModule/Readme.md)
        - 현재 위치 기반으로 POI 정보의 Lifecycle을 관리.
        - 지정된 범위를 간격으로 네트워크에 요청을 보내, POI 정보들을 Pooling하며, 이 직후, 각각 Lifecycle을 소유하게 됨.
        - SDK에서는 MonoBehaviour, IPOIEventHandler를 구현한 Manager 방식으로 관리할 수 있도록 설계
        - Setting Module을 통해, POI 인식까지 필요한 설정값 (e.g. 감지 범위, 폐기 범위 등)들을 조절할 수 있음.
    - Tracker.dll [(`참여`)](./TrackerModule/Readme.md)
        - Tracker 종류, Tracker 사용 타입에 따라, 설계되었음.
            - Naver Tracker (ANTracker)
            - Simulation Tracker
        - 서비스에서 직접 실행시킬 수도 있지만 Navigation이나 POI Module을 사용하는 서비스라면, 자동적으로 실행되도록 구현 (Dependency).
        - 동작시, 외부 (단말기) 혹은 내부 (Simulator Window)로부터 Texture를 요구하며, Input을 받아 들임. 정상 인식하게 되면, Success 상태 전환과 이벤트를 발행하여 연관된 서비스나 Module들이 동작하게 함 (e.g. 서버로부터 인식된 지역의 맵 모델과 POI를 불러옮).
        - 인식된 Texture를 바탕으로, Tracker는 현재 위치에 대한 정보를 Tracker 서버로부터 전달받음. 이와 동시에 ObjectDetection도 활성화가 되어, 객체 인식을 수행할 수 있게 됨. 내부 이벤트 핸들러를 통해, 객체 인식시 이벤트가 발행되도록 구현.
        - 실제 동작할 Tracker SDK의 헬퍼로 구성되어 있으며, Tracker의 실제 작동은 TrackerControllerBase를 통해 제어함. 즉, 현재 서비스 단계에서는 TrackerControllerBase를 Implement한 ANTrackerController와 SimulationTrackerController 두 가지로 구성이 됨.
        - Tracker Module을 통해, 서비스측에서 Tracker SDK의 Lifecycle에 맞는 이벤트를 구독할 수 있고, 이를 관리하는 역할을 수행.
    - Speech.dll [(`참여`)](./Chatbot/Readme.md)
        - 외부 TTS/STT 서비스를 이용하여 음성 인식, 출력을 서비스 쪽에서 쉽게 작업하도록 지원하는 Module.
        - 현재는 Naver CLOVA만 지원.
- Samples
    - NavWithPOI (`참여`)
        - 서비스 측에서 참고하여 네비게이션 및 POI 서비스를 개발할 수 있도록 만든 샘플.
    - ObjectDetection (`참여`)
        - 서비스 측에서 참고하여 객체 인식 서비스를 개발할 수 있도록 만든 샘플.
        - Tracker Module을 실행시키기 위해, 간단한 네비게이션 서비스도 포함시킴.
    - DataTree
        - 서버에 올라간 DB 내용을 서비스 측에서 Unity Editor로 확인할 수 있도록 만든 샘플.
        - 관리자는 Setting의 Token을 관리자 토큰으로 기입하여, CRUD가 가능.

## 참고

- 개발 가이드라인 문서 : [문서](./development%20guide.pdf)
- 시뮬레이터 소스
    - [개항장](https://drive.google.com/file/d/1OsHmRzUAlhMln1lIInjZ7Tpr64_SMpm0/view?usp=drive_link)
    - [송도](https://drive.google.com/file/d/1KjEgyXvDQTyWDEXG8cWbDNgI9OGmPHNW/view?usp=drive_link)
    - [부평역](https://drive.google.com/file/d/1cakYyo0jZKEgjac9Nex1OZBnFcoppC2C/view?usp=drive_link)
