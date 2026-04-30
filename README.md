# UE5 GestureOSCReceiver Plugin

[cite_start]**GestureOSCReceiver**는 실시간 AI 제스처 트래킹 데이터를 OSC 통신을 통해 수신하고, 이를 언리얼 엔진 5(UE5) 내에서 초저지연(Ultra-Low Latency)으로 실행하기 위해 설계된 경량 플러그인입니다[cite: 3, 4]. 

이 플러그인은 복잡한 머신러닝 추론 로직을 파이썬(Python)에 전담시키고, 언리얼 엔진은 오직 수신과 실행에만 집중하게 함으로써 최적의 퍼포먼스를 제공합니다.

---

### Key Features

* [cite_start]**Ultra-Low Latency (Fast Track):** 핵심 동작인 점프(주소: `/ue/motion/jump/`)는 C++ 네이티브 레벨에서 즉각적으로 낚아채어 블루프린트 가상 머신의 지연 없이 이벤트를 발생시킵니다[cite: 5].
* **Infinite Scalability (Raw Payload):** 그 외의 모든 제스처는 `FOSCMessage` 원본 패킷을 블루프린트로 통째로 넘겨줍니다. 개발자는 C++ 코드를 수정할 필요 없이 블루프린트에서 직접 `Int`, `Float`, `String`, `Bool` 등 모든 데이터 타입을 추출하여 확장할 수 있습니다.
* **Clean Architecture:** 수많은 `Get/Set` 노드와 네트워크 파싱 노드가 필요한 블루프린트 OSC 로직을 단 하나의 최적화된 C++ 컴포넌트로 대체합니다.
* **Easy Configuration:** 에디터의 디테일 패널에서 수신 IP 주소와 포트(기본값: 9000)를 간편하게 설정할 수 있습니다.

---

### Installation

1.  UE5 프로젝트 디렉토리 내의 `Plugins` 폴더로 이동합니다. (폴더가 없으면 생성하세요.)
2.  이 저장소를 `Plugins` 폴더 아래에 복사하거나 클론합니다 (예: `Plugins/GestureOSCReceiver/`).
3.  프로젝트의 `.uproject` 파일을 우클릭하고 **"Generate Visual Studio project files"**를 선택합니다.
4.  생성된 `.sln` 파일을 Visual Studio에서 열어 **Build**하거나, 언리얼 에디터를 실행하여 라이브 코딩으로 컴파일합니다.

---

### How to Use

#### 1. Attaching the Component
* 제스처로 제어하고 싶은 캐릭터 또는 액터 블루프린트를 엽니다.
* **+ Add** 버튼을 누르고 **Gesture Inference Component**를 추가합니다.
* `BeginPlay` 시 컴포넌트가 자동으로 설정된 IP와 포트로 OSC 서버를 생성하고 수신을 시작합니다.

#### 2. Configuration (Details Panel)
* **Receive IPAddress:** 수신할 로직의 IP 주소 (기본값: `172.30.1.69`).
* [cite_start]**Receive Port:** 통신에 사용할 UDP 포트 (기본값: `9000`)[cite: 5].

#### 3. Blueprint Events
컴포넌트를 선택한 후 디테일 패널 하단의 이벤트를 통해 로직을 구성합니다.

* **On Jump (Fast Track)**
    * `/ue/motion/jump/` 주소 수신 시 즉각 발생합니다.
    * [cite_start]**Outputs:** `Coordinates` (Float Array) - 빠른 공간 데이터 접근을 위한 좌표 배열을 제공합니다[cite: 7].
    * **Usage:** 언리얼 내장 `Jump` 함수 노드에 직접 연결하여 지연 없는 물리적 점프를 구현합니다.
* **On Gesture Received (General Track)**
    * 점프 외의 모든 OSC 주소 수신 시 발생합니다.
    * **Outputs:** `Address` (String), `Message Payload` (FOSCMessage).
    * **Usage:** `Address` 값을 `Switch on String` 노드에 연결하고, `Message Payload`에서 `Get OSC Message Integer at Index` 등 내장 노드를 사용하여 데이터를 자유롭게 추출합니다.

---

### Blueprint Routing Example

이 아키텍처를 통해 새로운 제스처를 추가할 때 C++ 재컴파일 없이 블루프린트만으로 확장이 가능합니다.

```text
[On Gesture Received] ---> [Switch on String]
                               |-- [/cjenm/gesture/hand_id] ---> [Get OSC Message Integer at Index] -> [Set Hand ID]
                               |-- [/cjenm/dyson]           ---> [Get OSC Message Boolean at Index] -> [Turn On Machine]
