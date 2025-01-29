---
layout: post
category: tutorials
---

[Ollama](https://ollama.com/) is open-source tool that allows to run a large language models (LLMs) locally.

Ollama offers out-of-box support for the Jetson platform with CUDA support, so you install Ollama with a single command.

## Install Ollama

You can run Ollama inside or outside (native) container. As we said before, Ollama's official installer support Jetson and can be easily install with the following command:

```bash
curl -fsSL https://ollama.com/install.sh | sh 
```

## Pull a model on CLI

```bash
ollama pull deepseek-r1:8b
```

Now, you can immediately use Ollama as interface for your model with the following command:

```
ollama run deepseek-r1:8b
```

Or you can use Open WebUI for a web interface.

[Open WebUI](https://github.com/open-webui/open-webui) is an user-friendly AI interface which support Ollama, OpenAI API, etc.

## Run Open WebUI in Docker container

You can use it with `docker`.

```bash
docker run -it --rm --network=host --add-host=host.docker.internal:host-gateway ghcr.io/open-webui/open-webui:main
```

Now, you can navigate your browser to `http://DEVICE_IP:8080`, you can create a fake account as these credentials are saved locally.

![ollama_deepseek_screenshot](https://raw.githubusercontent.com/reymor/reymor.github.io/master/files/pics/ollama_deepseek_screenshot.png)
