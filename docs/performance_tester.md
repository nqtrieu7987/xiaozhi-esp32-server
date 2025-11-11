# Performance Testing Tool Usage Guide for Speech Recognition, Large Language Model, Non-Streaming TTS, Streaming TTS, and Vision Model

1. Create a data directory under main/xiaozhi-server
2. Create a .config.yaml file in the data directory
3. In .data/config.yaml, write your parameters for speech recognition, large language model, streaming TTS, and vision model.
For example:
```
LLM:
  ChatGLMLLM:
    # Define LLM API type
    type: openai
    # glm-4-flash is free, but you still need to register and fill in the api_key
    # You can find your api key here https://bigmodel.cn/usercenter/proj-mgmt/apikeys
    model_name: glm-4-flash
    url: https://open.bigmodel.cn/api/paas/v4/
    api_key: your chat-glm web key

TTS:

VLLM:

ASR:
```
4. Run performance_tester.py in the main/xiaozhi-server directory:
```
python performance_tester.py
```
