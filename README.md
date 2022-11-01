# LocalPulse2Tibber
A writeup of how to make a Tibber Pulse work locally while still sending data to Tibber

##Whats needed
- A local MQTT Broker
- A Tibber Pulse
- Either a UART/Serial adapter or a modified version of the Tibber app (Only needed for setup, after that it can be uninstalled)
- An android phone (For app method)
- Basic knowledge of electronics

##App Method
1. Uninstall the official Tibber app
2. Install the custom Tibber app
3. Enable Developer Options on the phone https://developer.android.com/studio/debug/dev-options
4. Install ADB(Android Debug Bridge) https://forum.xda-developers.com/t/tool-minimal-adb-and-fastboot-2-9-18.2317790/
5. Connect your phone to your computer using a USB cable
6. Open Minimal ADB Fastboot![image](https://user-images.githubusercontent.com/7550920/199311336-8740d6c0-4cf7-498a-a08f-d5683a12daef.png)
7. Check that ADB is working correctly by doing `adb devices`.![image](https://user-images.githubusercontent.com/7550920/199311634-acd73d87-32f6-41b9-b913-df0e3960ce81.png)
9. Reset your Pulse by holding down the reset button for around 5 seconds
10. Disconnect the Pulse Power-Up from the Tibber app by going into Power-Ups > Pulse > Disconnect (If you have previously had it connected)
11. Execute `adb logcat -s "TibberPWN" -v raw`
12. Open the Tibber app and connect the Pulse normally. The command prompt window should spit out alot of stuff when you have successfully connnected the Pulse to Tibber![image](https://user-images.githubusercontent.com/7550920/199313661-2bd75a1a-da6f-4d18-82b0-c25fed2a11c5.png)
13. Press Ctrl + C inside the command prompt to make it stop listening for logcat messages
