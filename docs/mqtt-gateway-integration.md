# MQTT Gateway Deployment Tutorial

The `xiaozhi-esp32-server` project can be combined with Brother Xia's open-source [xiaozhi-mqtt-gateway](https://github.com/78/xiaozhi-mqtt-gateway) project for simple modification to achieve Xiaozhi hardware MQTT+UDP connection.
This tutorial is divided into three parts. You can choose the corresponding part to connect to the MQTT gateway according to whether you are using full module deployment or single module deployment:
- Part 1: Deploy MQTT Gateway
- Part 2: Full Module Running to Achieve Xiaozhi Hardware MQTT+UDP Connection
- Part 3: Single Module Running xiaozhi-server to Achieve Xiaozhi Hardware MQTT+UDP Connection

## Preparation Stage
Prepare your `xiaozhi-server`'s `mqtt-websocket` connection address. Based on your original `websocket address`, add the `?from=mqtt_gateway` string to get the `mqtt-websocket` connection address.

1. If you are using source code deployment, your `mqtt-websocket` address is:
```
ws://127.0.0.1:8000/xiaozhi/v1?from=mqtt_gateway
```

2. If you are using docker deployment, your `mqtt-websocket` address is:
```
ws://your host local network IP:8000/xiaozhi/v1?from=mqtt_gateway
```

## Important Notes

If you are deploying on a server, you need to ensure that ports `1883`, `8884`, and `8007` are open to the public. The protocol type for `8884` is `UDP`, and the others are `TCP`.

If you are deploying on a server, you need to ensure that ports `1883`, `8884`, and `8007` are open to the public. The protocol type for `8884` is `UDP`, and the others are `TCP`.

If you are deploying on a server, you need to ensure that ports `1883`, `8884`, and `8007` are open to the public. The protocol type for `8884` is `UDP`, and the others are `TCP`.


## Part 1: Deploy MQTT Gateway

1. Clone the [modified xiaozhi-mqtt-gateway project](https://github.com/xinnan-tech/xiaozhi-mqtt-gateway.git):
```bash
git clone https://ghfast.top/https://github.com/xinnan-tech/xiaozhi-mqtt-gateway.git
cd xiaozhi-mqtt-gateway
```

2. Install dependencies:
```bash
npm install
npm install -g pm2
```

3. Configure `config.json`:
```bash
cp config/mqtt.json.example config/mqtt.json
```

4. Edit the configuration file config/mqtt.json, replace the `mqtt-websocket` address from `Preparation Stage` in this document into `chat_servers`. For example, the source code deployment of `xiaozhi-server` is configured as follows:

``` 
{
    "production": {
        "chat_servers": [
            "ws://127.0.0.1:8000/xiaozhi/v1?from=mqtt_gateway"
        ]
    },
    "debug": false,
    "max_mqtt_payload_size": 8192,
    "mcp_client": {
        "capabilities": {
        },
        "client_info": {
            "name": "xiaozhi-mqtt-client",
            "version": "1.0.0"
        },
        "max_tools_count": 128
    }
}
```
5. Create a `.env` file in the project root directory and set the following environment variables:
```
PUBLIC_IP=your-ip         # Server public IP
MQTT_PORT=1883            # MQTT server port
UDP_PORT=8884             # UDP server port
API_PORT=8007             # Management API port
MQTT_SIGNATURE_KEY=test   # MQTT signature key
```
Please note the `PUBLIC_IP` configuration to ensure it matches the actual public IP. If you have a domain name, fill in the domain name.

`MQTT_SIGNATURE_KEY` is the key used for MQTT connection authentication. It's best to set it to something more complex, preferably 8 characters or more and containing both uppercase and lowercase letters. This key will be used later.

- Note: Do not use simple passwords like `123456`, `test`, etc.
- Note: Do not use simple passwords like `123456`, `test`, etc.
- Note: Do not use simple passwords like `123456`, `test`, etc.

6. Start MQTT Gateway
```
# Start service
pm2 start ecosystem.config.js

# View logs
pm2 logs xz-mqtt
```

When you see the following logs, it means the MQTT gateway has started successfully:
```
0|xz-mqtt  | 2025-09-11T12:14:48: MQTT server is listening on port 1883
0|xz-mqtt  | 2025-09-11T12:14:48: UDP server is listening on x.x.x.x:8884
```

If you need to restart the MQTT gateway, execute the following command:
```
pm2 restart xz-mqtt
```

## Part 2: Full Module Running to Achieve Xiaozhi Hardware MQTT+UDP Connection

Check the version number at the bottom of your Control Panel homepage to confirm whether your Control Panel version is `0.7.7` or above. If not, you need to upgrade the Control Panel.

1. In the Control Panel top, click `Parameter Management`, search for `server.mqtt_gateway`, click Edit, and fill in your `PUBLIC_IP` + `:` + `MQTT_PORT` set in the `.env` file. Similar to this:
```
192.168.0.7:1883
```
2. In the Control Panel top, click `Parameter Management`, search for `server.mqtt_signature_key`, click Edit, and fill in your `MQTT_SIGNATURE_KEY` set in the `.env` file.

3. In the Control Panel top, click `Parameter Management`, search for `server.udp_gateway`, click Edit, and fill in your `PUBLIC_IP` + `:` + `UDP_PORT` set in the `.env` file. Similar to this:
```
192.168.0.7:8884
```
4. In the Control Panel top, click `Parameter Management`, search for `server.mqtt_manager_api`, click Edit, and fill in your `PUBLIC_IP` + `:` + `UDP_PORT` set in the `.env` file. Similar to this:
```
192.168.0.7:8007
```

After the above configuration is complete, you can use the curl command to verify whether your OTA address will send down mqtt configuration. Change the `http://localhost:8002/xiaozhi/ota/` below to your OTA address:
```
curl 'http://localhost:8002/xiaozhi/ota/' \
  -H 'Content-Type: application/json' \
  -H 'Client-Id: 7b94d69a-9808-4c59-9c9b-704333b38aff' \
  -H 'Device-Id: 11:22:33:44:55:66' \
  --data-raw $'{\n  "application": {\n    "version": "1.0.1",\n    "elf_sha256": "1"\n  },\n  "board": {\n    "mac": "11:22:33:44:55:66"\n  }\n}'
```

If the returned content contains `mqtt` related configuration, it means the configuration is successful. Similar to this:

```
{"server_time":{"timestamp":1757567894012,"timeZone":"Asia/Shanghai","timezone_offset":480},"activation":{"code":"460609","message":"http://xiaozhi.server.com\n460609","challenge":"11:22:33:44:55:66"},"firmware":{"version":"1.0.1","url":"http://xiaozhi.server.com:8002/xiaozhi/otaMag/download/NOT_ACTIVATED_FIRMWARE_THIS_IS_A_INVALID_URL"},"websocket":{"url":"ws://192.168.4.23:8000/xiaozhi/v1/"},"mqtt":{"endpoint":"192.168.0.7:1883","client_id":"GID_default@@@11_22_33_44_55_66@@@7b94d69a-9808-4c59-9c9b-704333b38aff","username":"eyJpcCI6IjA6MDowOjA6MDowOjA6MSJ9","password":"Y8XP9xcUhVIN9OmbCHT9ETBiYNE3l3Z07Wk46wV9PE8=","publish_topic":"device-server","subscribe_topic":"devices/p2p/11_22_33_44_55_66"}}
```

Since MQTT information needs to be sent down through the OTA address, as long as you ensure you can normally connect to the server's OTA address, restart and wake up.

After waking up, pay attention to the mqtt-gateway logs to confirm if there are connection success logs.
```
pm2 logs xz-mqtt
```

## Part 3: Single Module Running to Achieve Xiaozhi Hardware MQTT+UDP Connection

Open your `data/.config.yaml` file, find `mqtt_gateway` under `server` and fill in your `PUBLIC_IP` + `:` + `MQTT_PORT` set in the `.env` file. Similar to this:
```
192.168.0.7:1883
```
Find `mqtt_signature_key` under `server` and fill in your `MQTT_SIGNATURE_KEY` set in the `.env` file.

Find `udp_gateway` under `server` and fill in your `PUBLIC_IP` + `:` + `UDP_PORT` set in the `.env` file. Similar to this:
```
192.168.0.7:8884
```

After the above configuration is complete, you can use the curl command to verify whether your OTA address will send down mqtt configuration. Change the `http://localhost:8002/xiaozhi/ota/` below to your OTA address:
```
curl 'http://localhost:8002/xiaozhi/ota/' \
  -H 'Device-Id: 11:22:33:44:55:66' \
  --data-raw $'{\n  "application": {\n    "version": "1.0.1",\n    "elf_sha256": "1"\n  },\n  "board": {\n    "mac": "11:22:33:44:55:66"\n  }\n}'
```

If the returned content contains `mqtt` related configuration, it means the configuration is successful. Similar to this:
```
{"server_time":{"timestamp":1758781561083,"timeZone":"GMT+08:00","timezone_offset":480},"activation":{"code":"527111","message":"http://xiaozhi.server.com\n527111","challenge":"11:22:33:44:55:66"},"firmware":{"version":"1.0.1","url":"http://xiaozhi.server.com:8002/xiaozhi/otaMag/download/NOT_ACTIVATED_FIRMWARE_THIS_IS_A_INVALID_URL"},"websocket":{"url":"ws://192.168.1.15:8000/xiaozhi/v1/"},"mqtt":{"endpoint":"192.168.1.15:1883","client_id":"GID_default@@@11_22_33_44_55_66@@@11_22_33_44_55_66","username":"eyJpcCI6IjE5Mi4xNjguMS4xNSJ9","password":"fjAYs49zTJecWqJ3jBt+kqxVn/x7vkXRAc85ak/va7Y=","publish_topic":"device-server","subscribe_topic":"devices/p2p/11_22_33_44_55_66"}}
```

Since MQTT information needs to be sent down through the OTA address, as long as you ensure you can normally connect to the server's OTA address, restart and wake up.

After waking up, pay attention to the mqtt-gateway logs to confirm if there are connection success logs.
```
pm2 logs xz-mqtt
```
