# open-webui Docker deploy

(本文档适用于 macOS, docker 使用官方的 docker-desktop, ChatGPT participated in the writing of this document)

open-webui ([https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui))

open-webui 是一款十分优秀的 ollama 前端,它可以让我们更方便的使用 ollama (具体请看官方文档)

## Deploy

open-webui 可以直接在本机上部署(Linux, Windows, macOS都行),但在本机自己配置环境与依赖太麻烦了,所以本文档选择直接用 docker 来部署

(现在可以先启动 ollama)

首先启动你的 docker ,然后开始拉镜像部署:

```zsh
docker run -d \
  -p 3000:8080 \
  -v open-webui:/app/backend/data \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  --name open-webui \
  --restart unless-stopped \
  ghcr.io/open-webui/open-webui:main
```

参数说明:

* `-p 3000:8080` → 把容器的 8080 端口映射到本机的 3000 端口

* `-v open-webui:/app/backend/data` → 创建或挂载一个 Docker 数据卷 `open-webui`,用来保存数据

* `-e OLLAMA_BASE_URL=http://host.docker.internal:11434` → 把 Docker 容器连到你本地 Ollama

* `--name open-webui` → 给这个容器起名为 `open-webui`，方便以后管理（比如停止、重启）

* `--restart unless-stopped` → 宿主机重启后自动启动（除非你主动停它）

如果不想要宿主机重启后自动启动,那不加`--restart unless-stopped`即可

`ghcr.io/open-webui/open-webui:main` 是 open-webui的官方 docker 镜像(官方仓库的 README 中有明确说明)

`host.docker.internal` 是 macOS 上让容器访问宿主机的通用方式(适用于 Docker Desktop)

在输入命令后如果出现 Unable to find image 'ghcr.io/open-webui/open-webui:main' locally main: Pulling from open-webui/open-webui , 并开始输出下载情况,便说明正常

等待镜像下载完成后, docker 会自动启动 `open-webui` 容器

这时可以访问 http://localhost:3000 试试

(首次访问该页面时,系统会提示你创建管理员帐户,无需手动配置用户)

⚠️ 以后在启动 open-webui 之前可以先将 ollama 启动

## Troubleshooting

但现在很有可能你会访问失败

如果访问失败,先检查容器状态:

```zsh
~ ❯ docker ps
CONTAINER ID   IMAGE                                COMMAND           CREATED              STATUS                          PORTS                                         NAMES
abc123def456   ghcr.io/open-webui/open-webui:main   "bash start.sh"   About a minute ago   Up About a minute (unhealthy)   0.0.0.0:3000->8080/tcp, [::]:3000->8080/tcp   open-webui
```

STATUS: Up About a minute (unhealthy) 说明 open-webui 启动失败或未完全就绪,健康检查未通过,所以访问失败

说明容器虽然“运行”了,但它内部的服务没能成功启动或对外提供服务,可能是：

* 后端 Flask / FastAPI 没启动

* 连接 Ollama 失败

* 端口未监听好

* 启动逻辑报错

然后检查日志 `docker logs open-webui` 看看有没有:

* Cannot connect to Ollama

* Connection refused

* Module not found

* RuntimeError

之类的报错

接着重启容器: `docker restart open-webui`

有时候也可以进入容器查看: `docker exec -it open-webui /bin/bash`

手动检查 `/app/backend/` 目录有没有服务，或者手动运行启动脚本调试
