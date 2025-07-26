# open-webui Docker deploy

(This document is applicable to macOS, docker uses the official docker-desktop, ChatGPT participated in the writing of this document)

open-webui ([https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui))

open-webui is a powerful and user-friendly front-end interface for Ollama, which allows us to use ollama more conveniently (please refer to the official document for details)

## Deploy

open-webui can be deployed directly on the local machine (Linux, Windows, macOS are all OK), but configuring the environment and dependencies locally is too troublesome, so this document opts for deployment via Docker.

(You can start ollama now)

First start your docker, and then start pulling the image deployment:

```zsh
docker run -d \
-p 3000:8080 \
-v open-webui:/app/backend/data \
-e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
--name open-webui \
--restart unless-stopped \
ghcr.io/open-webui/open-webui:main
```

Parameter description:

* `-p 3000:8080` → Map the container's port 8080 to the local port 3000

* `-v open-webui:/app/backend/data` → Create or mount a Docker data volume `open-webui` to save data

* `-e OLLAMA_BASE_URL=http://host.docker.internal:11434` → Connect the Docker container to your local Ollama

* `--name open-webui` → Name this container `open-webui` for easy management in the future (such as stopping and restarting)

* `--restart unless-stopped` → Automatically restart after the host is restarted (unless you stop it)

If you don't want it to automatically restart after the host is restarted, just don't add `--restart unless-stopped`

`ghcr.io/open-webui/open-webui:main` is the official docker image of open-webui (clearly stated in the README of the official repository)

`host.docker.internal` is a common way for containers to access the host on macOS (applicable to Docker Desktop)

After entering the command, if Unable to find image 'ghcr.io/open-webui/open-webui:main' locally main: Pulling from open-webui/open-webui appears and the download status starts to be output, it means it is normal.

Once the image is downloaded, Docker will automatically start the `open-webui` container.

You can visit http://localhost:3000 to try it out

(When you visit the page for the first time, you'll be prompted to create an admin account. No need to manually configure users.)

⚠️ In future sessions, start Ollama before launching open-webui

## Troubleshooting

But now you may fail to access

If access fails, check the container status first:

```zsh
~ ❯ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
abc123def456 ghcr.io/open-webui/open-webui:main "bash start.sh" About a minute ago Up About a minute (unhealthy) 0.0.0.0:3000->8080/tcp, [::]:3000->8080/tcp open-webui
```

STATUS: Up About a minute (unhealthy) Description open-webui failed to start or is not fully ready, and the health check failed, so the access failed

This means that although the container is "running", the services inside it failed to start successfully or provide services to the outside world. It may be:

* Backend Flask / FastAPI is not started

* Connection to Ollama failed

* Port is not monitored properly

* Startup logic error

Then check the log `docker logs open-webui` to see if there is:

* Cannot connect to Ollama

* Connection refused

* Module not found

* RuntimeError

and other errors

Then restart the container: `docker restart open-webui`

Sometimes you can also enter the container to check: `docker exec -it open-webui /bin/bash`

Manually check if there is a service in the `/app/backend/` directory, or manually run the startup script for debugging
