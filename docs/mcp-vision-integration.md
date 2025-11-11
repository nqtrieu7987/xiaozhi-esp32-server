# Vision Model Usage Guide
This tutorial is divided into two parts:
- Part 1: Single module running xiaozhi-server to enable vision model
- Part 2: How to enable vision model when running full module

Before enabling the vision model, you need to prepare three things:
- You need to prepare a device with a camera, and this device has already implemented the camera calling function in Brother Xia's repository. For example, `LC·Practical ESP32-S3 Development Board`
- Your device firmware version is upgraded to 1.6.6 and above
- You have successfully run the basic dialogue module

## Single Module Running xiaozhi-server to Enable Vision Model

### Step 1: Confirm Network
Since the vision model will start port 8003 by default.

If you are running with docker, please confirm whether your `docker-compose.yml` has exposed port `8003`. If not, update to the latest `docker-compose.yml` file.

If you are running with source code, confirm whether the firewall allows port `8003`

### Step 2: Select Your Vision Model
Open your `data/.config.yaml` file and set your `selected_module.VLLM` to a vision model. Currently we support vision models with `openai` type interfaces. `ChatGLMVLLM` is one of the models compatible with `openai`.

```
selected_module:
  VAD: ..
  ASR: ..
  LLM: ..
  VLLM: ChatGLMVLLM
  TTS: ..
  Memory: ..
  Intent: ..
```

Assuming we use `ChatGLMVLLM` as the vision model, we need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website to apply for an API key. If you have already applied for an API key before, you can reuse this key.

In your configuration file, add this configuration. If this configuration already exists, set your api_key.

```
VLLM:
  ChatGLMVLLM:
    api_key: your api_key
```

### Step 3: Start xiaozhi-server Service
If you are using source code, enter the command to start:
```
python app.py
```
If you are running with docker, restart the container:
```
docker restart xiaozhi-esp32-server
```

After startup, it will output logs with the following content:

```
2025-06-01 **** - OTA interface is           http://192.168.4.7:8003/xiaozhi/ota/
2025-06-01 **** - Vision analysis interface is        http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket address is       ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above address is a websocket protocol address, please do not access it with a browser=======
2025-06-01 **** - If you want to test websocket, please open test_page.html in the test directory with Google Chrome browser
2025-06-01 **** - =============================================================
```

After startup, use a browser to open the `Vision analysis interface` link in the logs. See what it outputs? If you are on Linux without a browser, you can execute this command:
```
curl -i your vision analysis interface
```

Normally it will display like this:
```
MCP Vision interface is running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on a public network, or deploying with docker, you must modify this configuration in your `data/.config.yaml`:
```
server:
  vision_explain: http://your-ip-or-domain:port/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be sent to the device. If your address is a local network address, or a docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this:

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally, and you have also tried to access the `Vision explanation interface address` sent down normally with a browser, please continue to the next step.

### Step 4: Device Wake-up and Enable

Say to the device "Please open the camera and tell me what you see"

Pay attention to the log output of xiaozhi-server to see if there are any errors.


## How to Enable Vision Model When Running Full Module

### Step 1: Confirm Network
Since the vision model will start port 8003 by default.

If you are running with docker, please confirm whether your `docker-compose_all.yml` has mapped port `8003`. If not, update to the latest `docker-compose_all.yml` file.

If you are running with source code, confirm whether the firewall allows port `8003`

### Step 2: Confirm Your Configuration File

Open your `data/.config.yaml` file and confirm whether the structure of your configuration file is the same as `data/config_from_api.yaml`. If not, or if something is missing, please add it.

### Step 3: Configure Vision Model API Key

We need to first log in to the [Zhipu AI](https://bigmodel.cn/usercenter/proj-mgmt/apikeys) website to apply for an API key. If you have already applied for an API key before, you can reuse this key.

Log in to the `Control Panel`, click `Model Configuration` in the top menu, click `Vision Large Language Model` in the left sidebar, find `VLLM_ChatGLMVLLM`, click the Modify button, enter your API key in the `API Key` field in the popup, and click Save.

After saving successfully, go to the agent you need to test, click `Configure Role`, and in the opened content, check whether `Vision Large Language Model (VLLM)` has selected the vision model just now. Click Save.

### Step 3: Start xiaozhi-server Module
If you are using source code, enter the command to start:
```
python app.py
```
If you are running with docker, restart the container:
```
docker restart xiaozhi-esp32-server
```

After startup, it will output logs with the following content:

```
2025-06-01 **** - Vision analysis interface is        http://192.168.4.7:8003/mcp/vision/explain
2025-06-01 **** - Websocket address is       ws://192.168.4.7:8000/xiaozhi/v1/
2025-06-01 **** - =======The above address is a websocket protocol address, please do not access it with a browser=======
2025-06-01 **** - If you want to test websocket, please open test_page.html in the test directory with Google Chrome browser
2025-06-01 **** - =============================================================
```

After startup, use a browser to open the `Vision analysis interface` link in the logs. See what it outputs? If you are on Linux without a browser, you can execute this command:
```
curl -i your vision analysis interface
```

Normally it will display like this:
```
MCP Vision interface is running normally, vision explanation interface address is: http://xxxx:8003/mcp/vision/explain
```

Please note, if you are deploying on a public network, or deploying with docker, you must modify this configuration in your `data/.config.yaml`:
```
server:
  vision_explain: http://your-ip-or-domain:port/mcp/vision/explain
```

Why? Because the vision explanation interface needs to be sent to the device. If your address is a local network address, or a docker internal address, the device cannot access it.

Assuming your public network address is `111.111.111.111`, then `vision_explain` should be configured like this:

```
server:
  vision_explain: http://111.111.111.111:8003/mcp/vision/explain
```

If your MCP Vision interface is running normally, and you have also tried to access the `Vision explanation interface address` sent down normally with a browser, please continue to the next step.

### Step 4: Device Wake-up and Enable

Say to the device "Please open the camera and tell me what you see"

Pay attention to the log output of xiaozhi-server to see if there are any errors.
