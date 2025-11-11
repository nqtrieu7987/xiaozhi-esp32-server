# IndexStreamTTS Usage Guide

## Environment Preparation
### 1. Clone Project 
```bash 
git clone https://github.com/Ksuriuri/index-tts-vllm.git
```
Enter the extracted directory:
```bash
cd index-tts-vllm
```
Switch to the specified version (using VLLM-0.10.2 historical version):
```bash
git checkout 224e8d5e5c8f66801845c66b30fa765328fd0be3
```

### 2. Create and Activate conda Environment
```bash 
conda create -n index-tts-vllm python=3.12
conda activate index-tts-vllm
```

### 3. Install PyTorch Version 2.8.0 Required (Latest Version)
#### Check the highest version supported by the graphics card and the actually installed version
```bash
nvidia-smi
nvcc --version
``` 
#### Highest CUDA Version Supported by Driver
```bash
CUDA Version: 12.8
```
#### Actually Installed CUDA Compiler Version
```bash
Cuda compilation tools, release 12.8, V12.8.89
```
#### Corresponding Installation Command (pytorch defaults to driver version 12.8)
```bash
pip install torch torchvision
```
PyTorch version 2.8.0 is required (corresponding to vllm 0.10.2). For specific installation instructions, please refer to: [pytorch official website](https://pytorch.org/get-started/locally/)

### 4. Install Dependencies
```bash 
pip install -r requirements.txt
```

### 5. Download Model Weights
### Option 1: Download Official Weight Files and Convert
These are official weight files. Download to any local path. Supports IndexTTS-1.5 weights  
| HuggingFace                                                   | ModelScope                                                          |
|---------------------------------------------------------------|---------------------------------------------------------------------|
| [IndexTTS](https://huggingface.co/IndexTeam/Index-TTS)        | [IndexTTS](https://modelscope.cn/models/IndexTeam/Index-TTS)        |
| [IndexTTS-1.5](https://huggingface.co/IndexTeam/IndexTTS-1.5) | [IndexTTS-1.5](https://modelscope.cn/models/IndexTeam/IndexTTS-1.5) |

The following uses ModelScope installation method as an example  
#### Please note: git needs to install and initialize lfs (can skip if already installed)
```bash
sudo apt-get install git-lfs
git lfs install
```
Create model directory and pull model:
```bash 
mkdir model_dir
cd model_dir
git clone https://www.modelscope.cn/IndexTeam/IndexTTS-1.5.git
```

#### Model Weight Conversion
```bash 
bash convert_hf_format.sh /path/to/your/model_dir
```
For example: If the IndexTTS-1.5 model you downloaded is stored in the model_dir directory, execute the following command:
```bash
bash convert_hf_format.sh model_dir/IndexTTS-1.5
```
This operation will convert the official model weights to a version compatible with the transformers library, saved in the vllm folder under the model weight path, making it convenient for the vllm library to load model weights later.

### 6. Modify Interface to Adapt to Project
The interface return data doesn't match the project and needs to be adjusted to directly return audio data
```bash
vi api_server.py
```
```bash 
@app.post("/tts", responses={
    200: {"content": {"application/octet-stream": {}}},
    500: {"content": {"application/json": {}}}
})
async def tts_api(request: Request):
    try:
        data = await request.json()
        text = data["text"]
        character = data["character"]

        global tts
        sr, wav = await tts.infer_with_ref_audio_embed(character, text)

        return Response(content=wav.tobytes(), media_type="application/octet-stream")
        
    except Exception as ex:
        tb_str = ''.join(traceback.format_exception(type(ex), ex, ex.__traceback__))
        print(tb_str)
        return JSONResponse(
            status_code=500,
            content={
                "status": "error",
                "error": str(tb_str)
            }
        )
```

### 7. Write sh Startup Script (Please note to run in the corresponding conda environment)
```bash 
vi start_api.sh
```
### Paste the following content and press :wq to save  
#### The /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 in the script should be modified to the actual path
```bash
# Activate conda environment
conda activate index-tts-vllm 
echo "Activate project conda environment"
sleep 2
# Find process number occupying port 11996
PID_VLLM=$(sudo netstat -tulnp | grep 11996 | awk '{print $7}' | cut -d'/' -f1)

# Check if process number is found
if [ -z "$PID_VLLM" ]; then
  echo "No process occupying port 11996 found"
else
  echo "Found process occupying port 11996, process number: $PID_VLLM"
  # Try normal kill first, wait 2 seconds
  kill $PID_VLLM
  sleep 2
  # Check if process is still running
  if ps -p $PID_VLLM > /dev/null; then
    echo "Process still running, force terminate..."
    kill -9 $PID_VLLM
  fi
  echo "Terminated process $PID_VLLM"
fi

# Find VLLM::EngineCore process
GPU_PIDS=$(ps aux | grep -E "VLLM|EngineCore" | grep -v grep | awk '{print $2}')

# Check if process number is found
if [ -z "$GPU_PIDS" ]; then
  echo "No VLLM related process found"
else
  echo "Found VLLM related process, process number: $GPU_PIDS"
  # Try normal kill first, wait 2 seconds
  kill $GPU_PIDS
  sleep 2
  # Check if process is still running
  if ps -p $GPU_PIDS > /dev/null; then
    echo "Process still running, force terminate..."
    kill -9 $GPU_PIDS
  fi
  echo "Terminated process $GPU_PIDS"
fi

# Create tmp directory (if it doesn't exist)
mkdir -p tmp

# Run api_server.py in background, redirect logs to tmp/server.log
nohup python api_server.py --model_dir /home/system/index-tts-vllm/model_dir/IndexTTS-1.5 --port 11996 > tmp/server.log 2>&1 &
echo "api_server.py is running in background, logs can be viewed in tmp/server.log"
```
Give script execution permission and run script:
```bash 
chmod +x start_api.sh
./start_api.sh
```
Logs will be output in tmp/server.log. You can view log status with the following command:
```bash
tail -f tmp/server.log
```
If GPU memory is sufficient, you can add startup parameters ----gpu_memory_utilization in the script to adjust GPU memory usage ratio, default value is 0.25

## Voice Configuration
index-tts-vllm supports registering custom voices through configuration files, supporting single voice and mixed voice configuration.  
Configure custom voices in the assets/speaker.json file in the project root directory
### Configuration Format Description
```bash
{
    "Speaker Name 1": [
        "Audio file path 1.wav",
        "Audio file path 2.wav"
    ],
    "Speaker Name 2": [
        "Audio file path 3.wav"
    ]
}
```
### Note (After configuring roles, you need to restart the service for voice registration)
After adding, you need to add the corresponding speaker in the Control Panel (single module then change the corresponding voice)
