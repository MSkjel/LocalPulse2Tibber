# LocalPulse2Tibber
A writeup of how to make a Tibber Pulse work locally while still sending data to Tibber.
This writeup is quite messy and could probably be cleaned up alot. Sorry for that, I tried including alot of pictures for reference

## Whats needed
- A local MQTT Broker
- A Tibber Pulse
- Either a UART/Serial adapter or a modified version of the Tibber app (Only needed for setup, after that it can be uninstalled)
- An android phone (For app method)
- Basic knowledge of electronics

# How to
1. Extract SSL/TLS Certificates using either: 
   - [ADB-App](#adb-app-method) 
   - [UART/Serial](#uartserial-method)
2. Setup MQTT Broker As Bridge [HomeAssistant Supervised](#setup-mqtt-broker-as-bridge) or [Other MQTT Broker](#using-local-mqtt-broker-not-hosted-on-homeassistant-supervised)
3. Connect Pulse to your local broker [Pulse Setup](#pulse-setup)
4. !!!!Make sure you uninstall the custom Tibber app after doing all this, or you wont receive any updates for the Tibber app!!!!!!

# Extract SSL/TLS Certificates
## ADB-App Method
1. Uninstall the official Tibber app
2. Install the custom Tibber app https://1drv.ms/u/s!AvNyZVxBVpmpmbtMHLgeRz9LsHWG3A
3. Enable Developer Options on the phone https://developer.android.com/studio/debug/dev-options
4. Open Developer Options and turn USB Debugging on.
5. Install ADB(Android Debug Bridge) https://forum.xda-developers.com/t/tool-minimal-adb-and-fastboot-2-9-18.2317790/
6. Connect your phone to your computer using a USB cable
7. Open Minimal ADB Fastboot
![image](https://user-images.githubusercontent.com/7550920/199311336-8740d6c0-4cf7-498a-a08f-d5683a12daef.png)
8. Check that ADB is working correctly by doing `adb devices`.
![image](https://user-images.githubusercontent.com/7550920/199311634-acd73d87-32f6-41b9-b913-df0e3960ce81.png)
9. Reset your Pulse by holding down the reset button for around 5 seconds
10. Disconnect the Pulse Power-Up from the Tibber app by going into Power-Ups > Pulse > Disconnect (If you have previously had it connected)
11. Execute `adb logcat -s "TibberPWN" -v raw`
12. Open the Tibber app and connect the Pulse normally. The command prompt window should spit out alot of stuff when you have successfully connnected the Pulse to Tibber. Make sure you forget the `Tibber Pulse` network on your phone if you have used it locally before, or the Tibber app wont find the Pulse
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

## App Method
1. Uninstall the official Tibber app
2. Install the custom Tibber app https://1drv.ms/u/s!AvNyZVxBVpmpmbtMHLgeRz9LsHWG3A
3. Reset your Pulse by holding down the reset button for around 5 seconds
4. Disconnect the Pulse Power-Up from the Tibber app by going into Power-Ups > Pulse > Disconnect (If you have previously had it connected)
5. Open the Tibber app and start setting up the Pulse. When you get to input your WiFi password, make sure to intensionally input the wrong password!
6. After doing that your app should show the success screen, but your pulse wont show up on the main Tibber app screen.
7. Connect to the Pulse's network, the password is shown on the back of the pulse. It looks like this `QWGH-ED12`. The - has to be included
8. Navigate to 10.133.70.1
9. The website should now include everything the Tibber app sent to the Pulse, including the wrong password.
![image](https://user-images.githubusercontent.com/7550920/199963269-72ceab2c-6f76-4e9d-a188-d7f8cbeecd7d.png)
10. Copy ca_cert, certificate and private_key and save them in three separate files on your computer(Use either right-click > select all or Ctrl+A to copy everything inside the fields!).
11. Take a not of the mqtt_url, mqtt_topic and optionally the mqtt_sub_topic(Used by Tibber to control the Pulse, but is not needed)
12. Change the psk to the correct password for your WiFi network.
13. Press send and then apply afterwards.
14. Check that the Pulse now shows on the main screen of the Tibber app(It wont have any data, just make sure the bubble is there. You might have to force-quit the app and restart it for it to show up).


## UART/Serial Method
//To come. Basically, the Pulse spits out everything the app sends to it when you connect it to the tibber app. It does require you to open the Pulse and connect a serial debugger to the TX/RX pins and make the pulse and the debugger share a common ground, but not power! The pulse needs to be connected to USB power while doing this The rough idea is to just listen for serial data while connecting it to the app. The output is in the same format you can see in step 12.

# Setup MQTT Broker As Bridge

## Using HomeAssistant Supervised
1. Make sure you have the MQTT addon installed
2. Make a user for the Pulse (If you have not made one already or you are allowing anonymous connections)
3. Open Studio Code Server or an equivalent File Editor on the HomeAssistant instance
4. Press the hamburger menu > File > Open Folder 
![image](https://user-images.githubusercontent.com/7550920/199342156-9585817d-3611-4f94-92b5-8e469a6f5890.png)
5. Enter `/root/` and press enter
![image](https://user-images.githubusercontent.com/7550920/199342265-46d148c6-8afb-46fa-b089-f7942b4c9d99.png)
6. Navigate to or create a directory called `share` if it doesnt exist.
7. Create a directory called `mosquitto` under `share`
8. Create a new directory called `tibber_cert` or something equivalent you remember under `mosquitto`
9. Create a new file called `bridge.conf` under `mosquitto`
Now the directory structure should look like this
![image](https://user-images.githubusercontent.com/7550920/199342856-16bedba8-b268-426b-9529-9a31be1e882f.png)
10. Move the CA.ca, Cert.crt and Priv.key files to the `tibber_cert` folder
![image](https://user-images.githubusercontent.com/7550920/199343069-aa4e202d-adbd-4c41-a564-e8bc1d17ce42.png)
11. Locate the Pulse-ID which can be found in the dump. Im not certain if the mqtt_url is always the same, but it is shown in the dump
![image](https://user-images.githubusercontent.com/7550920/199344323-614b9613-6dce-469c-ad3e-930783f23e0a.png)
12. Paste this into the bridge.conf file. (Make sure your mqtt_url is the same as mine, if not change `connection blabla:8883` to whatever you have in the code below) 
```connection bridge-to-tibber
bridge_cafile /share/mosquitto/tibber_cert/CA.ca
bridge_certfile /share/mosquitto/tibber_cert/Cert.crt
bridge_keyfile /share/mosquitto/tibber_cert/Priv.key
address a1zhmn1192zl1a.iot.eu-west-1.amazonaws.com:8883
clientid tibber-pulse-[Remove this and fill in your Pulse-ID]
try_private false
notifications false

topic $aws/# out
topic tibber-pulse-[Remove this and fill in your Pulse-ID]/receive in
``` 
It should look like this
![image](https://user-images.githubusercontent.com/7550920/199347703-a9374796-d92a-4317-a0f0-2240ca7ea236.png)
13. Open the Mosquitto addon Configuration
14. Set `active: true and folder: mosquitto` under Customize
![image](https://user-images.githubusercontent.com/7550920/199340791-758b5b1b-eae0-48cd-9631-88d64a8d0f96.png)
15. Save the configuration and restart the addon.
16. Make sure the log from the addon doesnt show any errors.

# Using local MQTT Broker not hosted on HomeAssistant Supervised
All the steps above are relevant. Just paste everything I have in the `.conf` file above into your MQTT Broker config file. And change the directories to some local directory.

# Pulse Setup
1. Reset the Pulse again, but DO NOT disconnect it in the Tibber app.
2. Connect to the Pulse's network, the password is shown on the back of the pulse. It looks like this `QWGH-ED12`. The - has to be included
3. Navigate to 10.133.70.1
4. Im assuming you already know how to connect the pulse to your local broker, but here are some pictures
Without MQTT username and password
![Screenshot_2022-11-01-22-32-40-03_40deb401b9ffe8e1df2f1cc5ba480b12](https://user-images.githubusercontent.com/7550920/199346611-61be22b1-a051-4e17-9bd3-0b092fbb002e.jpg)
With MQTT username and password
![Screenshot_2022-11-01-22-33-36-03_40deb401b9ffe8e1df2f1cc5ba480b12](https://user-images.githubusercontent.com/7550920/199346519-cf30afd7-6c6b-4dd6-bb6c-89f96b287d06.jpg)
5. The `mqtt_topic` on the Pulse has to be what is shown in the dump from the app/serial or else it wont work.
![image](https://user-images.githubusercontent.com/7550920/199346877-63ac2245-287b-47a9-be08-352fd2c4177c.png)
6. Press send, and apply.
7. Check that the pulse is connected to the MQTT Broker
8. Set the MQTT Topic your local Pulse decoder like https://github.com/toreamun/amshan-homeassistant or https://github.com/iotux/ElWiz to the MQTT Topic you specified over and in the dump from the app/serial.
