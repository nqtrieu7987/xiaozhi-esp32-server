# Deployment Architecture Diagram
![Please refer to - Full Module Installation Architecture Diagram](../docs/images/deploy2.png)
# Method 1: Docker Running Full Module
Starting from version `0.8.2`, the docker images released by this project only support `x86 architecture`. If you need to deploy on `arm64 architecture` CPUs, you can compile the `arm64 image` locally according to [this tutorial](docker-build.md).

## 1. Install Docker

If your computer doesn't have docker installed yet, you can install it according to the tutorial here: [docker installation](https://www.runoob.com/docker/ubuntu-docker-install.html)

There are two ways to install docker for full module. You can [use the lazy script](./Deployment_all.md#11-lazy-script) (author [@VanillaNahida](https://github.com/VanillaNahida))  
The script will automatically help you download the required files and configuration files. You can also use [manual deployment](./Deployment_all.md#12-manual-deployment) to build from scratch.



### 1.1 Lazy Script
Simple deployment, you can refer to the [video tutorial](https://www.bilibili.com/video/BV17bbvzHExd/). The text version tutorial is as follows:
> [!NOTE]  
> Currently only supports one-click deployment on Ubuntu servers. Other systems have not been tested and may have some strange bugs

Connect to the server using SSH tool and execute the following script with root privileges:
```bash
sudo bash -c "$(wget -qO- https://ghfast.top/https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/main/docker-setup.sh)"
```

The script will automatically complete the following operations:
> 1. Install Docker
> 2. Configure image source
> 3. Download/Pull images
> 4. Download speech recognition model files
> 5. Guide server configuration
>

After execution is complete and simple configuration, refer to [4. Run Program](#4-run-program) and [5. Restart xiaozhi-esp32-server](#5-restart-xiaozhi-esp32-server) for the 3 most important things mentioned. After completing these 3 configurations, you can use it.

### 1.2 Manual Deployment

#### 1.2.1 Create Directory

After installation, you need to find a directory to store the configuration files for this project. For example, we can create a new folder called `xiaozhi-server`.

After creating the directory, you need to create a `data` folder and a `models` folder under `xiaozhi-server`, and also create a `SenseVoiceSmall` folder under `models`.

The final directory structure is as follows:

```
xiaozhi-server
  ├─ data
  ├─ models
     ├─ SenseVoiceSmall
```

#### 1.2.2 Download Speech Recognition Model File

The speech recognition model of this project uses the `SenseVoiceSmall` model by default for speech-to-text conversion. Because the model is large, it needs to be downloaded separately. After downloading, place the `model.pt`
file in the `models/SenseVoiceSmall`
directory. Choose one of the following two download routes.

- Route 1: Download from Alibaba ModelScope [SenseVoiceSmall](https://modelscope.cn/models/iic/SenseVoiceSmall/resolve/master/model.pt)
- Route 2: Download from Baidu Netdisk [SenseVoiceSmall](https://pan.baidu.com/share/init?surl=QlgM58FHhYv1tFnUT_A8Sg&pwd=qvna) Extraction code:
  `qvna`


#### 1.2.3 Download Configuration Files

You need to download two configuration files: `docker-compose_all.yaml` and `config_from_api.yaml`. These files need to be downloaded from the project repository.

##### 1.2.3.1 Download docker-compose_all.yaml

Open [this link](../main/xiaozhi-server/docker-compose_all.yml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon, click the download button, and download the `docker-compose_all.yml` file. Download the file to your `xiaozhi-server` directory.

Or directly execute `wget https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/refs/heads/main/main/xiaozhi-server/docker-compose_all.yml` to download.

After downloading, return to this tutorial and continue.

##### 1.2.3.2 Download config_from_api.yaml

Open [this link](../main/xiaozhi-server/config_from_api.yaml) in your browser.

On the right side of the page, find the button named `RAW`. Next to the `RAW` button, find the download icon, click the download button, and download the `config_from_api.yaml` file. Download the file to the `data` folder under your `xiaozhi-server`, and then rename the `config_from_api.yaml` file to `.config.yaml`.

Or directly execute `wget https://raw.githubusercontent.com/xinnan-tech/xiaozhi-esp32-server/refs/heads/main/main/xiaozhi-server/config_from_api.yaml` to download and save.

After downloading the configuration files, let's confirm that the files in the entire `xiaozhi-server` are as follows:

```
xiaozhi-server
  ├─ docker-compose_all.yml
  ├─ data
    ├─ .config.yaml
  ├─ models
     ├─ SenseVoiceSmall
       ├─ model.pt
```

If your file directory structure is the same as above, continue. If not, check carefully to see if you missed any steps.

## 2. Backup Data

If you have successfully run the Control Panel before, and if it has saved your key information, please first copy important data from the Control Panel. Because during the upgrade process, the original data may be overwritten.

## 3. Clear Historical Version Images and Containers
Next, open the command line tool, use `Terminal` or `Command Line` tool to enter your `xiaozhi-server`, and execute the following commands:

```
docker compose -f docker-compose_all.yml down

docker stop xiaozhi-esp32-server
docker rm xiaozhi-esp32-server

docker stop xiaozhi-esp32-server-web
docker rm xiaozhi-esp32-server-web

docker stop xiaozhi-esp32-server-db
docker rm xiaozhi-esp32-server-db

docker stop xiaozhi-esp32-server-redis
docker rm xiaozhi-esp32-server-redis

docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:server_latest
docker rmi ghcr.nju.edu.cn/xinnan-tech/xiaozhi-esp32-server:web_latest
```

## 4. Run Program
Execute the following command to start the new version container:

```
docker compose -f docker-compose_all.yml up -d
```

After execution, execute the following command to view log information:

```
docker logs -f xiaozhi-esp32-server-web
```

When you see the output logs, it means your `Control Panel` has started successfully.

```
2025-xx-xx 22:11:12.445 [main] INFO  c.a.d.s.b.a.DruidDataSourceAutoConfigure - Init DruidDataSource
2025-xx-xx 21:28:53.873 [main] INFO  xiaozhi.AdminApplication - Started AdminApplication in 16.057 seconds (process running for 17.941)
http://localhost:8002/xiaozhi/doc.html
```

Please note that at this moment only the `Control Panel` can run. If port 8000 `xiaozhi-esp32-server` reports an error, ignore it for now.

At this time, you need to use a browser to open the `Control Panel`, link: http://127.0.0.1:8002, and register the first user. The first user is the super administrator, and subsequent users are regular users. Regular users can only bind devices and configure agents; super administrators can perform model management, user management, parameter configuration, and other functions.

Next, you need to do three important things:

### First Important Thing

Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the first data in the list, parameter code is `server.secret`, and copy it to `Parameter Value`.

`server.secret` needs to be explained. This `Parameter Value` is very important. Its function is to allow our `Server` end to connect to `manager-api`. `server.secret` is a key that is automatically randomly generated every time the manager module is deployed from scratch.

After copying the `Parameter Value`, open the `.config.yaml` file in the `data` directory under `xiaozhi-server`. At this moment, your configuration file content should be like this:

```
manager-api:
  url:  http://127.0.0.1:8002/xiaozhi
  secret: your server.secret value
```
1. Copy the `Parameter Value` of `server.secret` you just copied from the `Control Panel` to the `secret` in the `.config.yaml` file.

2. Since you are using docker deployment, change the `url` to the following `http://xiaozhi-esp32-server-web:8002/xiaozhi`

3. Since you are using docker deployment, change the `url` to the following `http://xiaozhi-esp32-server-web:8002/xiaozhi`

4. Since you are using docker deployment, change the `url` to the following `http://xiaozhi-esp32-server-web:8002/xiaozhi`

Similar to this effect:
```
manager-api:
  url: http://xiaozhi-esp32-server-web:8002/xiaozhi
  secret: 12345678-xxxx-xxxx-xxxx-123456789000
```

After saving, continue to do the second important thing.

### Second Important Thing

Log in to the Control Panel with super administrator account, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button,
After the modification box pops up, fill in the `Zhipu AI` key you registered into the `API Key` field. Then click Save.

## 5. Restart xiaozhi-esp32-server

Next, open the command line tool, use `Terminal` or `Command Line` tool and enter:
```
docker restart xiaozhi-esp32-server
docker logs -f xiaozhi-esp32-server
```
If you can see logs similar to the following, it indicates that the Server has started successfully.

```
25-02-23 12:01:09[core.websocket_server] - INFO - Websocket address is      ws://xxx.xx.xx.xx:8000/xiaozhi/v1/
25-02-23 12:01:09[core.websocket_server] - INFO - =======The above address is a websocket protocol address, please do not access it with a browser=======
25-02-23 12:01:09[core.websocket_server] - INFO - If you want to test websocket, please open test_page.html in the test directory with Google Chrome browser
25-02-23 12:01:09[core.websocket_server] - INFO - =======================================================
```

Since you are using full module deployment, you have two important interfaces that need to be written into esp32.

OTA interface:
```
http://your host local network ip:8002/xiaozhi/ota/
```

Websocket interface:
```
ws://your host ip:8000/xiaozhi/v1/
```

### Third Important Thing

Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the parameter code `server.websocket`, and enter your `Websocket interface`.

Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the parameter code `server.ota`, and enter your `OTA interface`.

Next, you can start operating your esp32 device. You can either `compile your own esp32 firmware` or configure to use `the firmware version 1.6.1 or above compiled by Brother Xia`. Choose one of the two:

1. [Compile your own esp32 firmware](firmware-build.md).

2. [Configure custom server based on firmware compiled by Brother Xia](firmware-setting.md).


# Method 2: Local Source Code Running Full Module

## 1. Install MySQL Database

If MySQL is already installed on this machine, you can directly create a database named `xiaozhi_esp32_server` in the database.

```sql
CREATE DATABASE xiaozhi_esp32_server CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

If you don't have MySQL yet, you can install mysql through docker:

```
docker run --name xiaozhi-esp32-server-db -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -e MYSQL_DATABASE=xiaozhi_esp32_server -e MYSQL_INITDB_ARGS="--character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci" -e TZ=Asia/Shanghai -d mysql:latest
```

## 2. Install redis

If you don't have Redis yet, you can install redis through docker:

```
docker run --name xiaozhi-esp32-server-redis -d -p 6379:6379 redis
```

## 3. Run manager-api Program

3.1 Install JDK21, set JDK environment variables

3.2 Install Maven, set Maven environment variables

3.3 Use Vscode programming tool, install Java environment related plugins

3.4 Use Vscode programming tool to load manager-api module

Configure database connection information in `src/main/resources/application-dev.yml`:

```
spring:
  datasource:
    username: root
    password: 123456
```
Configure Redis connection information in `src/main/resources/application-dev.yml`:
```
spring:
    data:
      redis:
        host: localhost
        port: 6379
        password:
        database: 0
```

3.5 Run Main Program

This project is a SpringBoot project. The startup method is:
Open `Application.java` and run the `Main` method to start:

```
Path address:
src/main/java/xiaozhi/AdminApplication.java
```

When you see the output logs, it means your `manager-api` has started successfully.

```
2025-xx-xx 22:11:12.445 [main] INFO  c.a.d.s.b.a.DruidDataSourceAutoConfigure - Init DruidDataSource
2025-xx-xx 21:28:53.873 [main] INFO  xiaozhi.AdminApplication - Started AdminApplication in 16.057 seconds (process running for 17.941)
http://localhost:8002/xiaozhi/doc.html
```

## 4. Run manager-web Program

4.1 Install nodejs

4.2 Use Vscode programming tool to load manager-web module

Enter the manager-web directory with terminal command:

```
npm install
```
Then start:
```
npm run serve
```

Please note, if your manager-api interface is not at `http://localhost:8002`, please modify the path in `main/manager-web/.env.development` during development.

After running successfully, you need to use a browser to open the `Control Panel`, link: http://127.0.0.1:8001, and register the first user. The first user is the super administrator, and subsequent users are regular users. Regular users can only bind devices and configure agents; super administrators can perform model management, user management, parameter configuration, and other functions.


Important: After successful registration, log in to the Control Panel with super administrator account, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button,
After the modification box pops up, fill in the `Zhipu AI` key you registered into the `API Key` field. Then click Save.

Important: After successful registration, log in to the Control Panel with super administrator account, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button,
After the modification box pops up, fill in the `Zhipu AI` key you registered into the `API Key` field. Then click Save.

Important: After successful registration, log in to the Control Panel with super administrator account, find `Model Configuration` in the top menu, then click `Large Language Model` in the left sidebar, find the first data `Zhipu AI`, click the `Modify` button,
After the modification box pops up, fill in the `Zhipu AI` key you registered into the `API Key` field. Then click Save.

## 5. Install Python Environment

This project uses `conda` to manage the dependency environment. If it's not convenient to install `conda`, you need to install `libopus` and `ffmpeg` according to your actual operating system.
If you decide to use `conda`, after installation, start executing the following commands.

Important note! Windows users can manage the environment by installing `Anaconda`. After installing `Anaconda`, search for `anaconda` related keywords in the `Start` menu,
find `Anaconda Prompt`, and run it as administrator. As shown below.

![conda_prompt](./images/conda_env_1.png)

After running, if you can see a `(base)` prefix in front of the command line window, it means you have successfully entered the `conda` environment. Then you can execute the following commands.

![conda_env](./images/conda_env_2.png)

```
conda remove -n xiaozhi-esp32-server --all -y
conda create -n xiaozhi-esp32-server python=3.10 -y
conda activate xiaozhi-esp32-server

# Add Tsinghua source channels
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge

conda install libopus -y
conda install ffmpeg -y

# When deploying in Linux environment, if you encounter errors similar to missing libiconv.so.2 dynamic library, please install it through the following command
conda install libiconv -y
```

Please note that the above commands are not executed all at once. You need to execute them step by step. After each step is executed, check the output logs to see if it was successful.

## 6. Install Project Dependencies

You first need to download the source code of this project. The source code can be downloaded through the `git clone` command. If you are not familiar with the `git clone` command.

you can open this address in your browser: `https://github.com/xinnan-tech/xiaozhi-esp32-server.git`

After opening, find a green button on the page that says `Code`, click it, and then you will see the `Download ZIP` button.

Click it to download the source code zip file of this project. After downloading to your computer, extract it. At this time, its name may be `xiaozhi-esp32-server-main`.
You need to rename it to `xiaozhi-esp32-server`. In this folder, enter the `main` folder, then enter `xiaozhi-server`. Please remember this directory `xiaozhi-server`.

```
# Continue using conda environment
conda activate xiaozhi-esp32-server
# Enter your project root directory, then enter main/xiaozhi-server
cd main/xiaozhi-server
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip install -r requirements.txt
```

### 7. Download Speech Recognition Model File

The speech recognition model of this project uses the `SenseVoiceSmall` model by default for speech-to-text conversion. Because the model is large, it needs to be downloaded separately. After downloading, place the `model.pt`
file in the `models/SenseVoiceSmall`
directory. Choose one of the following two download routes.

- Route 1: Download from Alibaba ModelScope [SenseVoiceSmall](https://modelscope.cn/models/iic/SenseVoiceSmall/resolve/master/model.pt)
- Route 2: Download from Baidu Netdisk [SenseVoiceSmall](https://pan.baidu.com/share/init?surl=QlgM58FHhYv1tFnUT_A8Sg&pwd=qvna) Extraction code:
  `qvna`

## 8. Configure Project Files

Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the first data in the list, parameter code is `server.secret`, and copy it to `Parameter Value`.

`server.secret` needs to be explained. This `Parameter Value` is very important. Its function is to allow our `Server` end to connect to `manager-api`. `server.secret` is a key that is automatically randomly generated every time the manager module is deployed from scratch.

If your `xiaozhi-server` directory doesn't have `data`, you need to create a `data` directory.
If your `data` directory doesn't have a `.config.yaml` file, you can copy the `config_from_api.yaml` file from the `xiaozhi-server` directory to `data` and rename it to `.config.yaml`.

After copying the `Parameter Value`, open the `.config.yaml` file in the `data` directory under `xiaozhi-server`. At this moment, your configuration file content should be like this:

```
manager-api:
  url: http://127.0.0.1:8002/xiaozhi
  secret: your server.secret value
```

Copy the `Parameter Value` of `server.secret` you just copied from the `Control Panel` to the `secret` in the `.config.yaml` file.

Similar to this effect:
```
manager-api:
  url: http://127.0.0.1:8002/xiaozhi
  secret: 12345678-xxxx-xxxx-xxxx-123456789000
```

## 5. Run Project

```
# Make sure to execute in the xiaozhi-server directory
conda activate xiaozhi-esp32-server
python app.py
```

If you can see logs similar to the following, it indicates that the service of this project has started successfully.

```
25-02-23 12:01:09[core.websocket_server] - INFO - Server is running at ws://xxx.xx.xx.xx:8000/xiaozhi/v1/
25-02-23 12:01:09[core.websocket_server] - INFO - =======The above address is a websocket protocol address, please do not access it with a browser=======
25-02-23 12:01:09[core.websocket_server] - INFO - If you want to test websocket, please open test_page.html in the test directory with Google Chrome browser
25-02-23 12:01:09[core.websocket_server] - INFO - =======================================================
```

Since you are using full module deployment, you have two important interfaces.

OTA interface:
```
http://your computer local network ip:8002/xiaozhi/ota/
```

Websocket interface:
```
ws://your computer local network ip:8000/xiaozhi/v1/
```

Please be sure to write the above two interface addresses into the Control Panel: they will affect websocket address distribution and automatic upgrade functions.

1. Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the parameter code `server.websocket`, and enter your `Websocket interface`.

2. Log in to the Control Panel with super administrator account, find `Parameter Management` in the top menu, find the parameter code `server.ota`, and enter your `OTA interface`.


Next, you can start operating your esp32 device. You can either `compile your own esp32 firmware` or configure to use `the firmware version 1.6.1 or above compiled by Brother Xia`. Choose one of the two:

1. [Compile your own esp32 firmware](firmware-build.md).

2. [Configure custom server based on firmware compiled by Brother Xia](firmware-setting.md).

# Common Issues
The following are some common issues for reference:

1. [Why does Xiaozhi recognize many Korean, Japanese, and English words when I speak?](./FAQ.md)<br/>
2. [Why does "TTS task error file does not exist" appear?](./FAQ.md)<br/>
3. [TTS often fails and times out](./FAQ.md)<br/>
4. [WiFi can connect to self-built server, but 4G mode cannot connect](./FAQ.md)<br/>
5. [How to improve Xiaozhi's dialogue response speed?](./FAQ.md)<br/>
6. [I speak slowly, and Xiaozhi always interrupts during pauses](./FAQ.md)<br/>
## Deployment Related Tutorials
1. [How to automatically pull the latest code of this project, automatically compile and start](./dev-ops-integration.md)<br/>
2. [How to deploy MQTT gateway to enable MQTT+UDP protocol](./mqtt-gateway-integration.md)<br/>
3. [How to integrate with Nginx](https://github.com/xinnan-tech/xiaozhi-esp32-server/issues/791)<br/>
## Extension Related Tutorials
1. [How to enable mobile phone number registration for the control panel](./ali-sms-integration.md)<br/>
2. [How to integrate HomeAssistant to achieve smart home control](./homeassistant-integration.md)<br/>
3. [How to enable vision model to achieve photo recognition](./mcp-vision-integration.md)<br/>
4. [How to deploy MCP endpoint](./mcp-endpoint-enable.md)<br/>
5. [How to connect to MCP endpoint](./mcp-endpoint-integration.md)<br/>
6. [How to enable voiceprint recognition](./voiceprint-integration.md)<br/>
7. [News plugin source configuration guide](./newsnow_plugin_config.md)<br/>
8. [Weather plugin usage guide](./weather-integration.md)<br/>
## Voice Cloning, Local Voice Deployment Related Tutorials
1. [How to clone voice in the control panel](./huoshan-streamTTS-voice-cloning.md)<br/>
2. [How to deploy and integrate index-tts local voice](./index-stream-integration.md)<br/>
3. [How to deploy and integrate fish-speech local voice](./fish-speech-integration.md)<br/>
4. [How to deploy and integrate PaddleSpeech local voice](./paddlespeech-deploy.md)<br/>
## Performance Testing Tutorials
1. [Component speed testing guide](./performance_tester.md)<br/>
2. [Regular public test results](https://github.com/xinnan-tech/xiaozhi-performance-research)<br/>
