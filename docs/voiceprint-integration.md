# Voiceprint Recognition Enable Guide

This tutorial contains 3 parts:
- 1. How to deploy the voiceprint recognition service
- 2. How to configure the voiceprint recognition interface when using full module deployment
- 3. How to configure voiceprint recognition when using simplified deployment

# 1. How to Deploy the Voiceprint Recognition Service

## Step 1: Download Voiceprint Recognition Project Source Code

Open the [Voiceprint Recognition Project Address](https://github.com/xinnan-tech/voiceprint-api) in your browser.

After opening, find a green button on the page that says `Code`, click it, and then you will see the `Download ZIP` button.

Click it to download the source code zip file of this project. After downloading to your computer, extract it. At this time, its name may be `voiceprint-api-main`.
You need to rename it to `voiceprint-api`.

## Step 2: Create Database and Table

Voiceprint recognition requires a `mysql` database. If you have already deployed the `Control Panel` before, it means you have already installed `mysql`. You can share it.

You can try using the `telnet` command on the host to see if you can normally access the `3306` port of `mysql`.
```
telnet 127.0.0.1 3306
```
If you can access port 3306, please ignore the following content and go directly to Step 3.

If you cannot access it, you need to recall how your `mysql` was installed.

If your mysql was installed by yourself using an installation package, it means your `mysql` has network isolation. You may need to solve the problem of accessing the `3306` port of `mysql` first.

If your `mysql` was installed through this project's `docker-compose_all.yml`. You need to find the `docker-compose_all.yml` file you used to create the database at that time and modify the following content:

Before modification:
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    expose:
      - "3306:3306"
```

After modification:
```
  xiaozhi-esp32-server-db:
    ...
    networks:
      - default
    ports:
      - "3306:3306"
```

Note: Change `expose` under `xiaozhi-esp32-server-db` to `ports`. After modification, you need to restart. The following are the commands to restart mysql:

```
# Enter the folder where your docker-compose_all.yml is located, for example, mine is xiaozhi-server
cd xiaozhi-server
docker compose -f docker-compose_all.yml down
docker compose -f docker-compose.yml up -d
```

After startup, use the `telnet` command on the host again to see if you can normally access the `3306` port of `mysql`.
```
telnet 127.0.0.1 3306
```
Normally, you should be able to access it this way.

## Step 3: Create Database and Table
If your host can normally access the mysql database, then create a database named `voiceprint_db` and a `voiceprints` table on mysql.

```
CREATE DATABASE voiceprint_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE voiceprint_db;

CREATE TABLE voiceprints (
    id INT AUTO_INCREMENT PRIMARY KEY,
    speaker_id VARCHAR(255) NOT NULL UNIQUE,
    feature_vector LONGBLOB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_speaker_id (speaker_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Step 4: Configure Database Connection

Enter the `voiceprint-api` folder and create a folder named `data`.

Copy the `voiceprint.yaml` from the root directory of `voiceprint-api` to the `data` folder and rename it to `.voiceprint.yaml`.

Next, you need to focus on configuring the database connection in `.voiceprint.yaml`.

```
mysql:
  host: "127.0.0.1"
  port: 3306
  user: "root"
  password: "your_password"
  database: "voiceprint_db"
```

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled in with your `local network IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled in with your `local network IP of the machine where mysql is located`.

Note! Since your voiceprint recognition service is deployed using docker, `host` needs to be filled in with your `local network IP of the machine where mysql is located`.

## Step 5: Start the Program
This project is a very simple project. It is recommended to use docker to run it. However, if you don't want to use docker to run it, you can refer to [this page](https://github.com/xinnan-tech/voiceprint-api/blob/main/README.md) to run it with source code. The following is the docker running method:

```
# Enter the root directory of this project source code
cd voiceprint-api

# Clear cache
docker compose -f docker-compose.yml down
docker stop voiceprint-api
docker rm voiceprint-api
docker rmi ghcr.nju.edu.cn/xinnan-tech/voiceprint-api:latest

# Start docker container
docker compose -f docker-compose.yml up -d
# View logs
docker logs -f voiceprint-api
```

At this time, the logs will output something similar to the following:
```
250711 INFO-🚀 Start: Production environment service startup (Uvicorn), listening address: 0.0.0.0:8005
250711 INFO-============================================================
250711 INFO-Voiceprint interface address: http://127.0.0.1:8005/voiceprint/health?key=abcd
250711 INFO-============================================================
```

Please copy the voiceprint interface address:

Since you are using docker deployment, you must not use the address above directly!

Since you are using docker deployment, you must not use the address above directly!

Since you are using docker deployment, you must not use the address above directly!

First copy the address and put it in a draft. You need to know what your computer's local network IP is. For example, my computer's local network IP is `192.168.1.25`, then:
My original interface address:
```
http://127.0.0.1:8005/voiceprint/health?key=abcd

```
Should be changed to:
```
http://192.168.1.25:8005/voiceprint/health?key=abcd
```

After modification, please use a browser to directly access the `Voiceprint interface address`. When the browser displays code similar to this, it means success:
```
{"total_voiceprints":0,"status":"healthy"}
```

Please keep the modified `Voiceprint interface address` for use in the next step.

# 2. How to Configure Voiceprint Recognition When Using Full Module Deployment

## Step 1: Configure Interface
If you are using full module deployment, log in to the Control Panel with administrator account, click `Parameter Dictionary` at the top, and select the `Parameter Management` function.

Then search for the parameter `server.voice_print`. At this time, its value should be `null`.
Click the Modify button and paste the `Voiceprint interface address` obtained in the previous step into the `Parameter Value` field. Then save.

If it can be saved successfully, everything is going well, and you can go to the agent to see the effect. If it's not successful, it means the Control Panel cannot access voiceprint recognition. It's very likely a network firewall issue, or the local network IP was not filled in correctly.

## Step 2: Set Agent Memory Mode

Enter your agent's role configuration and set the memory to `Local Short-term Memory`. You must enable `Report Text + Voice`.

## Step 3: Chat with Your Agent

Power on your device and chat with it at normal speech speed and tone.

## Step 4: Set Voiceprint

In the Control Panel, on the `Agent Management` page, in the agent's panel, there is a `Voiceprint Recognition` button. Click it. At the bottom, there is an `Add` button. You can register the voiceprint for a specific person's speech.
In the popup box, it is recommended to fill in the `Description` attribute, which can be the person's occupation, personality, hobbies. This helps the agent analyze and understand the speaker.

## Step 3: Chat with Your Agent

Power on your device and ask it, "Do you know who I am?" If it can answer, it means the voiceprint recognition function is working normally.

# 3. How to Configure Voiceprint Recognition When Using Simplified Deployment

## Step 1: Configure Interface
Open the `xiaozhi-server/data/.config.yaml` file (create if it doesn't exist), then add/modify the following content:

```
# Voiceprint recognition configuration
voiceprint:
  # Voiceprint interface address
  url: your voiceprint interface address
  # Speaker configuration: speaker_id, name, description
  speakers:
    - "test1,张三,张三是一个程序员"
    - "test2,李四,李四是一个产品经理"
    - "test3,王五,王五是一个设计师"
```

Paste the `Voiceprint interface address` obtained in the previous step into `url`. Then save.

Add the `speakers` parameter as needed. Note that this `speaker_id` parameter will be used later when registering voiceprints.

## Step 2: Register Voiceprint
If you have already started the voiceprint service, you can view the API documentation by accessing `http://localhost:8005/voiceprint/docs` in your local browser. Here we only explain how to use the API for registering voiceprints.

The API address for registering voiceprints is `http://localhost:8005/voiceprint/register`, and the request method is POST.

The request header needs to include Bearer Token authentication. The token is the part after `?key=` in the `Voiceprint interface address`. For example, if my voiceprint registration address is `http://127.0.0.1:8005/voiceprint/health?key=abcd`, then my token is `abcd`.

The request body contains the speaker ID (speaker_id) and a WAV audio file (file). The request example is as follows:

```
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -F "speaker_id=your_speaker_id_here" \
  -F "file=@/path/to/your/file" \
  http://localhost:8005/voiceprint/register
```

Here, `file` is the audio file of the speaker speaking that you want to register. `speaker_id` needs to be consistent with the `speaker_id` configured in the interface in Step 1. For example, if I need to register Zhang San's voiceprint, and Zhang San's `speaker_id` filled in `.config.yaml` is `test1`, then when I register Zhang San's voiceprint, the `speaker_id` filled in the request body is `test1`, and `file` is the audio file of Zhang San speaking a paragraph.

## Step 3: Start Service

Start the Xiaozhi server and voiceprint service, and you can use it normally.
