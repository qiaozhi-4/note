## 安装



### docker安装[详情](https://docs.openwebui.com/#open-webui-bundled-with-ollama)

启动

```sh
# 官方命令
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

# 将下载的模型挂载到本地 [E:\DockerVolumes\ollama]
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v E:\DockerVolumes\open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

```

