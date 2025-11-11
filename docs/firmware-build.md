# ESP32 Firmware Compilation

## Step 1: Prepare Your OTA Address

If you are using version 0.3.12 of this project, whether it's a simple Server deployment or full module deployment, you will have an OTA address.

Since simple Server deployment and full module deployment have different OTA address setup methods, please choose the specific method below:

### If You Are Using Simple Server Deployment
At this point, please open your OTA address in a browser. For example, my OTA address is:
```
http://192.168.1.25:8003/xiaozhi/ota/
```
If it displays "OTA interface is running normally, the websocket address sent to the device is: ws://xxx:8000/xiaozhi/v1/"

You can use the project's built-in `test_page.html` to test if you can connect to the websocket address output by the OTA page.

If you cannot access it, you need to modify the `server.websocket` address in the configuration file `.config.yaml`, restart, and test again until `test_page.html` can access normally.

After success, please proceed to Step 2.

### If You Are Using Full Module Deployment
At this point, please open your OTA address in a browser. For example, my OTA address is:
```
http://192.168.1.25:8002/xiaozhi/ota/
```

If it displays "OTA interface is running normally, websocket cluster count: X", then proceed to Step 2.

If it displays "OTA interface is not running normally", it's probably because you haven't configured the `Websocket` address in the `Control Panel`. Then:

- 1. Log in to the Control Panel as super administrator

- 2. Click `Parameter Management` in the top menu

- 3. Find the `server.websocket` item in the list and enter your `Websocket` address. For example, mine is:

```
ws://192.168.1.25:8000/xiaozhi/v1/
```

After configuration, refresh your OTA interface address in the browser to see if it's normal. If it's still not normal, confirm again whether the Websocket has started normally and whether the Websocket address has been configured.

## Step 2: Configure Environment
First configure the project environment according to this tutorial: [《Windows Setup ESP IDF 5.3.2 Development Environment and Compile Xiaozhi》](https://icnynnzcwou8.feishu.cn/wiki/JEYDwTTALi5s2zkGlFGcDiRknXf)

## Step 3: Open Configuration File
After configuring the compilation environment, download Brother Xia's xiaozhi-esp32 project source code,

Download Brother Xia's [xiaozhi-esp32 project source code](https://github.com/78/xiaozhi-esp32) from here.

After downloading, open the `xiaozhi-esp32/main/Kconfig.projbuild` file.

## Step 4: Modify OTA Address

Find the `default` content of `OTA_URL`, change `https://api.tenclass.net/xiaozhi/ota/`
    to your own address. For example, if my interface address is `http://192.168.1.25:8002/xiaozhi/ota/`, change the content to this.

Before modification:
```
config OTA_URL
    string "Default OTA URL"
    default "https://api.tenclass.net/xiaozhi/ota/"
    help
        The application will access this URL to check for new firmwares and server address.
```
After modification:
```
config OTA_URL
    string "Default OTA URL"
    default "http://192.168.1.25:8002/xiaozhi/ota/"
    help
        The application will access this URL to check for new firmwares and server address.
```

## Step 4: Set Compilation Parameters

Set compilation parameters:

```
# Enter the xiaozhi-esp32 root directory in terminal command line
cd xiaozhi-esp32
# For example, the board I use is esp32s3, so set the compilation target to esp32s3. If your board is another model, please replace it with the corresponding model
idf.py set-target esp32s3
# Enter menu configuration
idf.py menuconfig
```

After entering menu configuration, enter `Xiaozhi Assistant`, and set `BOARD_TYPE` to your board's specific model.
Save and exit, return to the terminal command line.

## Step 5: Compile Firmware

```
idf.py build
```

## Step 6: Package bin Firmware

```
cd scripts
python release.py
```

After the above packaging command is executed, the firmware file `merged-binary.bin` will be generated in the `build` directory under the project root directory.
This `merged-binary.bin` is the firmware file to be burned to the hardware.

Note: If you encounter a "zip" related error after executing the second command, please ignore this error. As long as the firmware file `merged-binary.bin` is generated in the `build` directory, it won't have much impact on you. Please continue.

## Step 7: Burn Firmware
   Connect the esp32 device to the computer, use Chrome browser to open the following URL:

```
https://espressif.github.io/esp-launchpad/
```

Open this tutorial, [Flash Tool/Web End Firmware Burning (No IDF Development Environment)](https://ccnphfhqs21z.feishu.cn/wiki/Zpz4wXBtdimBrLk25WdcXzxcnNS).
Scroll to: `Method 2: ESP-Launchpad Browser WEB End Burning`, start from `3. Burn Firmware/Download to Development Board`, and follow the tutorial to operate.

After successful burning and successful network connection, wake up Xiaozhi through the wake word and pay attention to the console information output by the server.

## Common Issues
The following are some common issues for reference:

[1. Why does Xiaozhi recognize many Korean, Japanese, and English words when I speak?](./FAQ.md)

[2. Why does "TTS task error file does not exist" appear?](./FAQ.md)

[3. TTS often fails and times out](./FAQ.md)

[4. WiFi can connect to self-built server, but 4G mode cannot connect](./FAQ.md)

[5. How to improve Xiaozhi's dialogue response speed?](./FAQ.md)

[6. I speak slowly, and Xiaozhi always interrupts during pauses](./FAQ.md)

[7. I want to control lights, air conditioners, remote power on/off, etc. through Xiaozhi](./FAQ.md)
