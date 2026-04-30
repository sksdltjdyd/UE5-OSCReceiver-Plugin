# UE5 GestureOSCReceiver Plugin

**GestureOSCReceiver** is an ultra-lightweight, high-performance Unreal Engine 5 (UE5) plugin designed to receive real-time AI gesture tracking data via OSC and execute actions with near-zero latency. 

This plugin delegates complex Machine Learning inference logic entirely to Python, allowing Unreal Engine to focus solely on data reception and execution, thereby providing optimal performance.

---

### Key Features

* **Ultra-Low Latency (Fast Track):** Critical actions like jumping (address: `/ue/motion/jump/`) are intercepted natively at the C++ level, triggering events instantly without the delay of the Blueprint Virtual Machine.
* **Infinite Scalability (Raw Payload):** For all other gestures, the plugin passes the raw `FOSCMessage` packet directly to Blueprints. Developers can extract any data type (`Int`, `Float`, `String`, `Bool`, etc.) directly in Blueprints without needing to recompile or modify C++ code.
* **Clean Architecture:** Replaces messy Blueprint OSC parsing networks (which typically require numerous `Get/Set` nodes) with a single, highly optimized C++ component.
* [cite_start]**Easy Configuration:** Easily configure the listening IP address and port (default: 9000) directly from the Editor's Details Panel[cite: 5].

---

### Installation

1. Navigate to the `Plugins` folder inside your UE5 project directory. (Create the folder if it does not exist.)
2. Copy or clone this repository into the `Plugins` folder (e.g., `Plugins/GestureOSCReceiver/`).
3. Right-click your project's `.uproject` file and select **"Generate Visual Studio project files"**.
4. Open the generated `.sln` file in Visual Studio and **Build** the project, or simply launch the Unreal Editor to compile the plugin via Live Coding.

---

### How to Use

#### 1. Attaching the Component
* Open the Character or Actor Blueprint you want to control with gestures.
* Click the **+ Add** button and attach the **Gesture Inference Component**.
* On `BeginPlay`, the component will automatically create an OSC server with the configured IP and port, and start listening.

#### 2. Configuration (Details Panel)
* **Receive IPAddress:** The IP address to listen on (Default: `172.30.1.69`).
* [cite_start]**Receive Port:** The UDP port used for OSC communication (Default: `9000`)[cite: 5].

#### 3. Blueprint Events
Select the component and configure your logic using the events available at the bottom of the Details Panel:

* **On Jump (Fast Track)**
    * Fires instantly upon receiving the `/ue/motion/jump/` OSC address.
    * **Outputs:** `Coordinates` (Float Array) - Provides quick access to spatial coordinate data.
    * **Usage:** Connect directly to Unreal's native `Jump` function node for zero-delay physical execution.
* **On Gesture Received (General Track)**
    * Fires upon receiving any OSC address other than the jump address.
    * **Outputs:** `Address` (String), `Message Payload` (FOSCMessage).
    * **Usage:** Route the logic by connecting the `Address` to a `Switch on String` node. Extract your required data types by connecting the `Message Payload` to UE5's native nodes (e.g., `Get OSC Message Integer at Index`, `Get OSC Message Boolean at Index`).

---

### Blueprint Routing Example

With this architecture, expanding your gesture library is handled entirely within Blueprints. No C++ recompilation is required!

```text
[On Gesture Received] ---> [Switch on String]
                               |-- [/cjenm/gesture/hand_id] ---> [Get OSC Message Integer at Index] -> [Set Hand ID]
                               |-- [/cjenm/dyson]           ---> [Get OSC Message Boolean at Index] -> [Turn On Machine]
