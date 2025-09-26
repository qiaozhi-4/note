## 安装



### docker安装[详情](https://hub.docker.com/r/ollama/ollama)

启动

```sh
# 官方命令
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama

# 将下载的模型挂载到本地 [E:\DockerVolumes\ollama]
docker run -d --gpus=all -v E:\DockerVolumes\ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama

```

