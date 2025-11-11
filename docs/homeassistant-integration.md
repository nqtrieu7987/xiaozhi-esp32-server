# Xiaozhi ESP32 - Open Source Server and HomeAssistant Integration Guide

[TOC]

-----

## Introduction

This document will guide you on how to integrate ESP32 devices with HomeAssistant.

## Prerequisites

- `HomeAssistant` is installed and configured
- The model I chose this time is: Free ChatGLM, which supports functioncall function calls

## Operations Before Starting (Required)

### 1. Get HA's Network Address Information

Please visit your Home Assistant network address. For example, my HA's address is 192.168.4.7, and the port is the default 8123, then open in the browser:

```
http://192.168.4.7:8123
```

> Manual method to query HA's IP address** (only when xiaozhi-esp32-server and HA are deployed on the same network device [e.g., same wifi]):**
>
> 1. Enter Home Assistant (frontend).
>
> 2. Click **Settings** in the bottom left corner → **System** → **Network**.
>
> 3. Scroll to the bottom `Home Assistant website` area, in `local network`, click the `eye` button to see the currently used IP address (such as `192.168.1.10`) and network interface. Click `copy link` to copy directly.
>
>    ![image-20250504051716417](images/image-ha-integration-01.png)

Or, if you have already set up a directly accessible Home Assistant OAuth address, you can also directly access it in the browser:

```
http://homeassistant.local:8123
```

### 2. Log in to `Home Assistant` to Get Development Key

Log in to `HomeAssistant`, click `Bottom left corner avatar -> Personal`, switch to the `Security` navigation bar, scroll to the bottom `Long-lived access tokens` to generate an api_key, and copy and save it. The following methods all need to use this api key and it only appears once (small tip: You can save the generated QR code image, and you can scan the QR code later to extract the api key again).

## Method 1: HomeAssistant Call Function Built by Xiaozhi Community

### Function Description

- If you need to add new devices later, this method requires manually restarting the `xiaozhi-esp32-server service` to update device information** (Important)**.

- You need to ensure that `Xiaomi Home` has been integrated in HomeAssistant, and Mi Home devices have been imported into `HomeAssistant`.

- You need to ensure that the `xiaozhi-esp32-server Control Panel` can be used normally.

- My `xiaozhi-esp32-server Control Panel` and `HomeAssistant` are deployed on another port of the same machine, version is `0.3.10`

  ```
  http://192.168.4.7:8002
  ```


### Configuration Steps

#### 1. Log in to `HomeAssistant` to Organize the List of Devices to Control

Log in to `HomeAssistant`, click `Settings in the bottom left corner`, then enter `Devices & Services`, and then click `Entities` at the top.

Then search for the switches you want to control in entities. After the results come out, click one of the results in the list, and a switch interface will appear.

In the switch interface, we try to click the switch to see if it will turn on/off as we click. If it can be operated, it means it's normally connected.

Then find the settings button in the switch panel, click it, and you can view the `Entity ID` of this switch.

We open a notepad and organize a piece of data in this format:

Location + English comma + Device name + English comma + `Entity ID` + English semicolon

For example, I'm at the company, I have a toy light, its identifier is switch.cuco_cn_460494544_cp1_on_p_2_1, then write this piece of data:

```
公司,玩具灯,switch.cuco_cn_460494544_cp1_on_p_2_1;
```

Of course, in the end I may need to operate two lights, my final result is:

```
公司,玩具灯,switch.cuco_cn_460494544_cp1_on_p_2_1;
公司,台灯,switch.iot_cn_831898993_socn1_on_p_2_1;
```

This string, we call it "Device List String" and need to save it well, it will be useful later.

#### 2. Log in to `Control Panel`

![image-20250504051716417](images/image-ha-integration-06.png)

Log in to the `Control Panel` with administrator account. In `Agent Management`, find your agent, and then click `Configure Role`.

Set Intent Recognition to `Function Call` or `LLM Intent Recognition`. At this time, you will see an `Edit Functions` button on the right. Click the `Edit Functions` button, and a `Function Management` box will pop up.

In the `Function Management` box, you need to check `HomeAssistant Device Status Query` and `HomeAssistant Device Status Modification`.

After checking, click `HomeAssistant Device Status Query` in `Selected Functions`, and then configure your `HomeAssistant` address, key, and device list string in `Parameter Configuration`.

After editing, click `Save Configuration`. At this time, the `Function Management` box will be hidden. Then click Save Agent Configuration again.

After saving successfully, you can wake up the device to operate.

#### 3. Wake Up Device to Control

Try saying to esp32, "Turn on XXX light"

## Method 2: Xiaozhi Uses Home Assistant's Voice Assistant as LLM Tool

### Function Description

- This method has a relatively serious disadvantage - **This method cannot use the function_call plugin capabilities of the Xiaozhi open source ecosystem**, because using Home Assistant as Xiaozhi's LLM tool will transfer intent recognition capabilities to Home Assistant. However, **this method can experience native Home Assistant operation functions, and Xiaozhi's chat capabilities remain unchanged**. If you really mind, you can use [Method 3](##Method 3: Use Home Assistant's MCP Service (Recommended)), which is also supported by Home Assistant, which can maximize the experience of Home Assistant functions.

### Configuration Steps:

#### 1. Configure Home Assistant's Large Model Voice Assistant.

**You need to configure Home Assistant's voice assistant or large model tool in advance.**

#### 2. Get Home Assistant's Language Assistant's Agent ID.

1. Enter the Home Assistant page. Click `Developer Tools` on the left.
2. In the opened `Developer Tools`, click the `Actions` tab (as shown in operation 1 in the figure), in the `Actions` option bar on the page, find or enter `conversation.process (Conversation - Process)` and select `Conversation (conversation): Process` (as shown in operation 2 in the figure).

![image-20250504043539343](images/image-ha-integration-02.png)

3. Check the `Agent` option on the page, and in the `Conversation Agent` that becomes always on, select the voice assistant name you configured in step one. As shown in the figure, what I configured is `ZhipuAi` and select it.

![image-20250504043854760](images/image-ha-integration-03.png)

4. After selecting, click `Enter YAML Mode` at the bottom left of the form.

![image-20250504043951126](images/image-ha-integration-04.png)

5. Copy the agent-id value in it. For example, in the figure, mine is `01JP2DYMBDF7F4ZA2DMCF2AGX2` (for reference only).

![image-20250504044046466](images/image-ha-integration-05.png)

6. Switch to the `config.yaml` file of the Xiaozhi open source server `xiaozhi-esp32-server`, find Home Assistant in the LLM configuration, set your Home Assistant network address, Api key and the agent_id you just queried.
7. Modify the `LLM` of the `selected_module` attribute in the `config.yaml` file to `HomeAssistant`, and `Intent` to `nointent`.
8. Restart the Xiaozhi open source server `xiaozhi-esp32-server` to use it normally.

## Method 3: Use Home Assistant's MCP Service (Recommended)

### Function Description

- You need to integrate and install the HA integration - [Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/) in Home Assistant in advance.

- This method and Method 2 are both solutions provided by HA officially. Different from Method 2, you can normally use the open source plugins built by the Xiaozhi open source server `xiaozhi-esp32-server`, while allowing you to freely use any LLM large model that supports function_call functionality.

### Configuration Steps

#### 1. Install Home Assistant's MCP Service Integration.

Integration official website - [Model Context Protocol Server](https://www.home-assistant.io/integrations/mcp_server/)..

Or follow the manual operations below.

> - Go to the Home Assistant page **[Settings > Devices & Services](https://my.home-assistant.io/redirect/integrations)**.
>
> - In the bottom right corner, select the **[Add Integration](https://my.home-assistant.io/redirect/config_flow_start?domain=mcp_server)** button.
>
> - Select **Model Context Protocol Server** from the list.
>
> - Follow the on-screen instructions to complete the setup.

#### 2. Configure Xiaozhi Open Source Server MCP Configuration Information


Enter the `data` directory and find the `.mcp_server_settings.json` file.

If your `data` directory doesn't have a `.mcp_server_settings.json` file,
- Please copy the `mcp_server_settings.json` file from the `xiaozhi-server` folder root directory to the `data` directory and rename it to `.mcp_server_settings.json`
- Or [download this file](https://github.com/xinnan-tech/xiaozhi-esp32-server/blob/main/main/xiaozhi-server/mcp_server_settings.json), download it to the `data` directory and rename it to `.mcp_server_settings.json`


Modify this part of the content in `"mcpServers"`:

```json
"Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://YOUR_HA_HOST/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "YOUR_API_ACCESS_TOKEN"
      }
},
```

Notes:

1. **Replace Configuration:**
   - Replace `YOUR_HA_HOST` in `args` with your HA service address. If your service address already contains https/http (e.g., `http://192.168.1.101:8123`), you only need to fill in `192.168.1.101:8123`.
   - Replace `YOUR_API_ACCESS_TOKEN` in `env`'s `API_ACCESS_TOKEN` with the development key api key you obtained earlier.
2. **If you add configuration and there are no new `mcpServers` configurations after the `"mcpServers"` bracket, you need to remove the last comma `,`, otherwise parsing may fail.**

**Final effect reference (for reference):**

```json
 "mcpServers": {
    "Home Assistant": {
      "command": "mcp-proxy",
      "args": [
        "http://192.168.1.101:8123/mcp_server/sse"
      ],
      "env": {
        "API_ACCESS_TOKEN": "abcd.efghi.jkl"
      }
    }
  }
```

#### 3. Configure Xiaozhi Open Source Server System Configuration

1. **Select any LLM large model that supports function_call as Xiaozhi's LLM chat assistant (but do not select Home Assistant as LLM tool)**. The model I chose this time is: Free ChatGLM, which supports functioncall function calls, but sometimes the calls are not very stable. If you want to pursue stability, it is recommended to set LLM to: DoubaoLLM, using the specific model_name: doubao-1-5-pro-32k-250115.

2. Switch to the `config.yaml` file of the Xiaozhi open source server `xiaozhi-esp32-server`, set your LLM large model configuration, and adjust the `Intent` of the `selected_module` configuration to `function_call`.

3. Restart the Xiaozhi open source server `xiaozhi-esp32-server` to use it normally.
