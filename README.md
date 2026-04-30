UE5 GestureOSCReceiver Plugin
GestureOSCReceiver is an ultra-lightweight, high-performance Unreal Engine 5 (UE5) plugin designed to receive real-time AI gesture tracking data via OSC and execute actions with near-zero latency.
This plugin is highly optimized for the real-time interaction phase of an AI gesture recognition pipeline (MediaPipe -> OSC -> UE5). By delegating heavy Machine Learning inference logic entirely to Python, this receiver focuses purely on maximum network speed and infinite Blueprint scalability.  

Key Features

Ultra-Low Latency (Fast Track): C++ natively intercepts critical actions like jumping (address /ue/motion/jump/ ) and triggers the event instantly, bypassing standard Blueprint parsing delays.  

Infinite Scalability (Raw Payload): For all other generic gestures, it passes the raw FOSCMessage directly to Blueprints. This empowers developers to extract any data type (Float, Int, String, Bool) on the fly without ever modifying the C++ source code.

Clean Architecture: Replaces messy, complex Blueprint OSC parsing networks with a single, highly optimized C++ component.


Easy Configuration: Configure the listening IP Address and Port easily from the Details Panel (Default: Port 9000 ).  

Installation
Navigate to the Plugins folder inside your UE5 project directory. (Create the folder if it does not exist).

Copy or clone this repository into the Plugins folder (e.g., Plugins/GestureOSCReceiver/).

Right-click your project's .uproject file and select "Generate Visual Studio project files".

Open the .sln file in Visual Studio and Build the project, or simply launch the Unreal Editor to compile the plugin via Live Coding.

How to Use
1. Attaching the Component
Open the Character, PlayerController, or GameMode Blueprint you want to control.

Click the + Add button and attach the Gesture OSC Receiver Component.

On BeginPlay, the component will automatically open the OSC Server and start listening for incoming AI data.

2. Configuration (Details Panel)
Receive IPAddress: The local or network IP address to listen to (Default: 172.30.1.69).


Receive Port: The UDP port used for OSC communication (Default: 9000).  

3. Blueprint Events
Select the component and click the green + button in the Details panel to add the following events:

On Jump (Fast Track)

Fires instantly when the /ue/motion/jump/ OSC address is received.  

Outputs: Coordinates (Float Array) - Provides quick access to spatial data if needed.

Usage: Connect directly to the UE5 native Jump function node for zero-delay physical execution.

On Gesture Received (General Track)

Fires for any other incoming OSC address.

Outputs: Address (String), Message Payload (FOSCMessage).

Usage: Route the logic by connecting the Address to a Switch on String node. Then, extract your required data types by connecting the Message Payload to UE5's native nodes (e.g., Get OSC Message Integer at Index, Get OSC Message Boolean at Index).

Blueprint Routing Example
With this architecture, expanding your gesture library is completely done in Blueprints. No C++ recompilation required!

Plaintext
[On Gesture Received] ---> [Switch on String]
                               |-- [/cjenm/gesture/hand_id] ---> [Get OSC Message Integer at Index] -> [Set Hand ID]
                               |-- [/cjenm/dyson]           ---> [Get OSC Message Boolean at Index] -> [Turn On Machine]
