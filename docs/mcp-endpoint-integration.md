# MCP Endpoint Usage Guide

This tutorial uses Brother Xia's open-source mcp calculator function as an example to introduce how to connect your custom mcp service to your own endpoint.

The premise of this tutorial is that your `xiaozhi-server` has already enabled the mcp endpoint function. If you haven't enabled it yet, you can first enable it according to [this tutorial](./mcp-endpoint-enable.md).

# How to Connect a Simple MCP Function to an Agent, Such as Calculator Function

### If You Are Using Full Module Deployment
If you are using full module deployment, you can enter the Control Panel, Agent Management, click `Configure Role`, and on the right side of `Intent Recognition`, there is an `Edit Functions` button.

Click this button. In the popup page, at the bottom, there will be `MCP Endpoint`. Normally, it will display this agent's `MCP Endpoint Address`. Next, let's extend a calculator function based on MCP technology for this agent.

This `MCP Endpoint Address` is very important, you will use it later.

### If You Are Using Single Module Deployment
If you are using single module deployment and you have already configured the MCP endpoint address in the configuration file, then normally when the single module deployment starts, it will output logs similar to the following:
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

As above, the `ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc` in the output `mcp endpoint is` is your `MCP Endpoint Address`.

This `MCP Endpoint Address` is very important, you will use it later.

## Step 1: Download Brother Xia's MCP Calculator Project Code

Open Brother Xia's [Calculator Project](https://github.com/78/mcp-calculator) in your browser.

After opening, find a green button on the page that says `Code`, click it, and then you will see the `Download ZIP` button.

Click it to download the source code zip file of this project. After downloading to your computer, extract it. At this time, its name may be `mcp-calculatorr-main`.
You need to rename it to `mcp-calculator`. Next, we use the command line to enter the project directory and install dependencies:


```bash
# Enter project directory
cd mcp-calculator

conda remove -n mcp-calculator --all -y
conda create -n mcp-calculator python=3.10 -y
conda activate mcp-calculator

pip install -r requirements.txt
```

## Step 2: Start

Before starting, copy the MCP endpoint address from your Control Panel agent.

For example, my agent's mcp address is:
```
ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
```

Start entering commands:

```bash
export MCP_ENDPOINT=ws://192.168.1.25:8004/mcp_endpoint/mcp/?token=abc
```

After entering, start the program:

```bash
python mcp_pipe.py calculator.py
```

### If You Are Using Control Panel Deployment
If you are using Control Panel deployment, after starting, enter the Control Panel again, click Refresh MCP Connection Status, and you will see your extended function list.

### If You Are Using Single Module Deployment
If you are using single module deployment, when the device connects, it will output logs similar to the following, indicating success:

```
250705 -INFO-Initializing MCP endpoint: wss://2662r3426b.vicp.fun/mcp_e 
250705 -INFO-Sending MCP endpoint initialization message
250705 -INFO-MCP endpoint connection successful
250705 -INFO-MCP endpoint initialization successful
250705 -INFO-Unified tool handler initialization complete
250705 -INFO-MCP endpoint server information: name=Calculator, version=1.9.4
250705 -INFO-Number of tools supported by MCP endpoint: 1
250705 -INFO-All MCP endpoint tools obtained, client ready
250705 -INFO-Tool cache refreshed
250705 -INFO-Current supported function list: [ 'get_time', 'get_lunar', 'play_music', 'get_weather', 'handle_exit_intent', 'calculator']
```
If it includes `'calculator'`, it means the device can call the calculator tool based on intent recognition.
