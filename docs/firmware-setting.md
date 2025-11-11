# Configure Custom Server Based on Firmware Compiled by Brother Xia

## Step 1: Confirm Version
Burn the [firmware version 1.6.1 or above](https://github.com/78/xiaozhi-esp32/releases) compiled by Brother Xia

## Step 2: Prepare Your OTA Address
If you follow the tutorial and use full module deployment, you should have an OTA address.

At this point, please open your OTA address in a browser. For example, my OTA address is:
```
https://2662r3426b.vicp.fun/xiaozhi/ota/
```

If it displays "OTA interface is running normally, websocket cluster count: X", then continue.

If it displays "OTA interface is not running normally", it's probably because you haven't configured the `Websocket` address in the `Control Panel`. Then:

- 1. Log in to the Control Panel as super administrator

- 2. Click `Parameter Management` in the top menu

- 3. Find the `server.websocket` item in the list and enter your `Websocket` address. For example, mine is:

```
wss://2662r3426b.vicp.fun/xiaozhi/v1/
```

After configuration, refresh your OTA interface address in the browser to see if it's normal. If it's still not normal, confirm again whether the Websocket has started normally and whether the Websocket address has been configured.

## Step 3: Enter Network Configuration Mode
Enter the device's network configuration mode. At the top of the page, click "Advanced Options", enter your server's `ota` address, and click Save. Restart the device.
![Please refer to - OTA Address Settings](../docs/images/firmware-setting-ota.png)

## Step 4: Wake Up Xiaozhi and Check Log Output

Wake up Xiaozhi and see if the logs are outputting normally.


## Common Issues
The following are some common issues for reference:

[1. Why does Xiaozhi recognize many Korean, Japanese, and English words when I speak?](./FAQ.md)

[2. Why does "TTS task error file does not exist" appear?](./FAQ.md)

[3. TTS often fails and times out](./FAQ.md)

[4. WiFi can connect to self-built server, but 4G mode cannot connect](./FAQ.md)

[5. How to improve Xiaozhi's dialogue response speed?](./FAQ.md)

[6. I speak slowly, and Xiaozhi always interrupts during pauses](./FAQ.md)

[7. I want to control lights, air conditioners, remote power on/off, etc. through Xiaozhi](./FAQ.md)
