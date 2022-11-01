# LocalPulse2Tibber
A writeup of how to make a Tibber Pulse work locally while still sending data to Tibber

## Whats needed
- A local MQTT Broker
- A Tibber Pulse
- Either a UART/Serial adapter or a modified version of the Tibber app (Only needed for setup, after that it can be uninstalled)
- An android phone (For app method)
- Basic knowledge of electronics

## App Method
1. Uninstall the official Tibber app
2. Install the custom Tibber app
3. Enable Developer Options on the phone https://developer.android.com/studio/debug/dev-options
4. Install ADB(Android Debug Bridge) https://forum.xda-developers.com/t/tool-minimal-adb-and-fastboot-2-9-18.2317790/
5. Connect your phone to your computer using a USB cable
6. Open Minimal ADB Fastboot
![image](https://user-images.githubusercontent.com/7550920/199311336-8740d6c0-4cf7-498a-a08f-d5683a12daef.png)
7. Check that ADB is working correctly by doing `adb devices`.![image](https://user-images.githubusercontent.com/7550920/199311634-acd73d87-32f6-41b9-b913-df0e3960ce81.png)
9. Reset your Pulse by holding down the reset button for around 5 seconds
10. Disconnect the Pulse Power-Up from the Tibber app by going into Power-Ups > Pulse > Disconnect (If you have previously had it connected)
11. Execute `adb logcat -s "TibberPWN" -v raw`
12. Open the Tibber app and connect the Pulse normally. The command prompt window should spit out alot of stuff when you have successfully connnected the Pulse to Tibber
![image](https://user-images.githubusercontent.com/7550920/199313661-2bd75a1a-da6f-4d18-82b0-c25fed2a11c5.png)
13. Press Ctrl + C inside the command prompt to make it stop listening for logcat messages
14. Scroll up until you see the first -----BEGIN CERTIFICATE-----
15. Copy everything from -----BEGIN CERTIFICATE----- to -----END CERTIFICATE----- including themselves ![image](https://user-images.githubusercontent.com/7550920/199314779-1e1bebd6-746c-4068-ae12-b45e0f3e8665.png) 
And paste it into a new file called CA.ca
![image](https://user-images.githubusercontent.com/7550920/199314987-33924d01-3957-4b79-bf7d-60bee55a292f.png)
I'm using VSCode here. But all editors should work, as long as you can choose what filetype it should be saved as
16. Do the same for the next certificate. But call the file Cert.crt
![image](https://user-images.githubusercontent.com/7550920/199315754-7bc944cf-f261-45b4-9081-d2afc137827b.png)
![image](https://user-images.githubusercontent.com/7550920/199316395-20319b27-e132-4415-aec0-c8ba923d9709.png)
17. Do the same thing for the private key. But call the file Priv.key
![image](https://user-images.githubusercontent.com/7550920/199316041-fd7cd324-4e45-4541-b484-9874313a4433.png)
![image](https://user-images.githubusercontent.com/7550920/199316459-79fe3035-195d-474f-aa8c-d10d47946078.png)

It's really important that the files dont have any formatting errors. They should look excactly as mine do.
You should now have a folder looking like this
![image](https://user-images.githubusercontent.com/7550920/199316687-f7bc972e-109c-4ed8-ac8a-710cb4f68d83.png)

