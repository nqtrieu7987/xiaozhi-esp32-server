# MCP Endpoint Deployment and Usage Guide

This tutorial contains 3 parts:
- 1. How to deploy the MCP endpoint service
- 2. How to configure the MCP endpoint when using full module deployment
- 3. How to configure the MCP endpoint when using single module deployment

# 1. How to Deploy the MCP Endpoint Service

## Step 1: Download MCP Endpoint Project Source Code

Open the [MCP endpoint project address](https://github.com/xinnan-tech/mcp-endpoint-server) in your browser.

After opening, find a green button on the page that says `Code`, click it, and then you will see the `Download ZIP` button.

Click it to download the source code zip file of this project. After downloading to your computer, extract it. At this time, its name may be `mcp-endpoint-server-main`.
You need to rename it to `mcp-endpoint-server`.

## Step 2: Start the Program
This project is a very simple project. It is recommended to use docker to run it. However, if you don't want to use docker to run it, you can refer to [this page](https://github.com/xinnan-tech/mcp-endpoint-server/blob/main/README_dev.md) to run it with source code. The following is the docker running method:

```
# Enter the root directory of this project source code
cd mcp-endpoint-server

# Clear cache
docker compose -f docker-compose.yml down
docker stop mcp-endpoint-server
docker rm mcp-endpoint-server
docker rmi ghcr.nju.edu.cn/xinnan-tech/mcp-endpoint-server:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f mcp-endpoint-server
```

At this time, the logs will output something similar to the following:
```
250705 INFO-=====The following addresses are Control Panel/Single Module MCP Endpoint Addresses====
250705 INFO-Control Panel MCP Parameter Configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
250705 INFO-Single Module Deployment MCP Endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
250705 INFO-=====Please select according to specific deployment, do not leak to anyone======
```

Please copy the two interface addresses:

Since you are using docker deployment, you must not use the addresses above directly!

Since you are using docker deployment, you must not use the addresses above directly!

Since you are using docker deployment, you must not use the addresses above directly!

First copy the addresses and put them in a draft. You need to know what your computer's local network IP is. For example, my computer's local network IP is `192.168.1.25`, then:
My original interface addresses:
```
Control Panel MCP Parameter Configuration: http://172.22.0.2:8004/mcp_endpoint/health?key=abc
Single Module Deployment MCP Endpoint: ws://172.22.0.2:8004/mcp_endpoint/mcp/?token=def
```
Should be changed to:
```
Control Panel MCP Parameter Configuration: http://192.168.1.25:8004/mcp_endpoint/health?key=abc
Single Module Deployment MCP Endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After modification, please use a browser to directly access the `Control Panel MCP Parameter Configuration` address. When the browser displays code similar to this, it means success:
```
{"result":{"status":"success","connections":{"tool_connections":0,"robot_connections":0,"total_connections":0},"error":null,"id":null,"jsonrpc":"2.0"}
```

Please keep the above two `interface addresses` for use in the next step.

# 2. How to Configure MCP Endpoint When Using Full Module Deployment

If you are using full module deployment, log in to the Control Panel with administrator account, click `Parameter Dictionary` at the top, and select the `Parameter Management` function.

Then search for the parameter `server.mcp_endpoint`. At this time, its value should be `null`.
Click the Modify button and paste the `Control Panel MCP Parameter Configuration` obtained in the previous step into the `Parameter Value` field. Then save.

If it can be saved successfully, everything is going well, and you can go to the agent to see the effect. If it's not successful, it means the Control Panel cannot access the mcp endpoint. It's very likely a network firewall issue, or the local network IP was not filled in correctly.

# 3. How to Configure MCP Endpoint When Using Single Module Deployment

If you are using single module deployment, find your configuration file `data/.config.yaml`.
Search for `mcp_endpoint` in the configuration file. If not found, add the `mcp_endpoint` configuration. Similar to mine:
```
server:
  websocket: ws://your-ip-or-domain:port/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here..

mcp_endpoint: your endpoint websocket address
```
At this time, please paste the `Single Module Deployment MCP Endpoint` obtained in `How to Deploy the MCP Endpoint Service` into `mcp_endpoint`. Similar to this:

```
server:
  websocket: ws://your-ip-or-domain:port/xiaozhi/v1/
  http_port: 8002
log:
  log_level: INFO

# There may be more configurations here

mcp_endpoint: ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=def
```

After configuration, starting the single module will output logs similar to the following:
```
250705[__main__]-INFO-Initialized component: vad successful SileroVAD
250705[__main__]-INFO-Initialized component: asr successful FunASRServer
250705[__main__]-INFO-OTA interface is          http://192.168.1.25:8002/xiaozhi/ota/
250705[__main__]-INFO-Vision analysis interface is     http://192.168.1.25:8002/mcp/vision/explain
250705[__main__]-INFO-mcp endpoint is        ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
250705[__main__]-INFO-Websocket address is    ws://192.168.1.25:8000/xiaozhi/v1/
250705[__main__]-INFO-=======The above address is a websocket protocol address, please do not access it with a browser=======
250705[__main__]-INFO-If you want to test websocket, please open test_page.html in the test directory with Google Chrome browser
250705[__main__]-INFO-=============================================================
```

As above, if it can output something similar to `mcp endpoint is` with `ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc`, it means the configuration is successful.

