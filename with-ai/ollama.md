# Running local models with Ollama

- [Running local models with Ollama](#running-local-models-with-ollama)
  - [What is Ollama?](#what-is-ollama)
  - [Use MLX Models on Apple Silicon Macs](#use-mlx-models-on-apple-silicon-macs)
  - [How to Identify MLX Models](#how-to-identify-mlx-models)
  - [Downloading and running Ollama models](#downloading-and-running-ollama-models)
  - [Ollama CLI](#ollama-cli)
  - [Integrating Ollama with VS Code](#integrating-ollama-with-vs-code)
  - [Friends Don't Let Friends Use Ollama (April 15, 2026 by Zetaphor)](#friends-dont-let-friends-use-ollama-april-15-2026-by-zetaphor)

## What is Ollama?

Ollama is a software tool designed for managing LLMs on local devices. This tool allows users to run sophisticated language models, such as LLaMA (Large Language Model Meta AI), on their own machines rather than relying solely on cloud-based solutions.
By running models locally, Ollama ensures that sensitive data remains on the user's device, enhancing privacy and security compared to cloud-based services. Users maintain full ownership and control over their data, which is particularly important for sensitive or proprietary information.

Developers and researchers can use Ollama to experiment with and fine-tune LLMs without the need for cloud resources. Running models locally can be more cost-effective in the long run, as it eliminates the need for continuous cloud service subscriptions. However, local installations require ongoing maintenance and updates, which can be more complex compared to managed cloud services.

To run LLMs locally, users need powerful hardware, typically including high-end CPUs, significant RAM, and often graphics processing units (GPUs) to handle the intensive computation. Running models locally reduces latency, which is critical for applications requiring immediate responses. LLMs are resource-intensive, and not all users may have the necessary hardware to run them efficiently.

Ollama aims to simplify the installation and setup process, providing tools and documentation to help users get started with minimal friction.

## Use MLX Models on Apple Silicon Macs

When running Ollama on Apple Silicon Macs (M1-M5), it is important to choose models optimized for MLX.

MLX is Apple’s native machine learning framework, designed to take full advantage of the unified memory architecture and integrated CPU and GPU. Models built for MLX run significantly more efficiently than generic formats such as GGUF, which often rely more heavily on the CPU.

In practice, MLX models deliver faster response times, smoother token generation, and better overall system efficiency. They also make better use of available memory, allowing larger models to run more reliably without slowdowns or excessive swapping.

Using non-MLX models on a Mac will still work, but performance is noticeably worse and does not reflect the true capability of the hardware.

For best results on macOS with Apple Silicon, always prefer MLX-optimized models where available.

## How to Identify MLX Models

On Apple Silicon Macs, the easiest way to spot an MLX model is by how it is labeled and distributed.

MLX models typically:
- Explicitly mention `MLX` in the name or description
- Are hosted in repositories that reference Apple Silicon or MLX
- Use formats associated with MLX rather than GGUF

Non-MLX models (the default most people use):
- Are usually in `GGUF` format
- Come from the `llama.cpp` ecosystem
- Do not mention MLX at all

MLX example: Llama 3
- Often labeled like: `Llama-3-8B-Instruct-MLX`
- Typical source: MLX community repos such as mlx-community on Hugging Face

Non-MLX example: Llama 3 GGUF (used by Ollama by default)
- Often labeled like: `Llama-3-8B-Instruct.Q4_K_M.gguf`
- Runs via llama.cpp, works everywhere, including Mac, but not optimized for it
- This is what most users end up running without realizing there’s a better option for macOS.

> Good practice: If you see `MLX` in the model name then it’s optimized for Mac. If you see `.gguf` then it’s not MLX

## Downloading and running Ollama models

To quickly download and run an Ollama model in interactive mode, use the following command:
```shell
ollama run <model>
```

The most common models supported by Ollama include those shown in the following table:

Model|Parameters|Size
---|---|---
`deepseek-r1`|7 B|4.7 GB
`llama3.3`|70 B|40 GB
`llama3.2`|3 B|2.0 GB
`llama3.1`|8 B|4.9 GB
`codellama`|7 B|3.8 GB
`llama2-uncensored`|7 B|3.8 GB
`phi3`|3.8 B|2.3 GB
`mistral`|7 B|4.1 GB
`gemma:7b`|7 B|4.8 GB
`gemma:2b`|2 B|1.4 GB

We will use Meta’s Llama 3.1 model for its versatility and to maximize the likelihood that it will run okay on your computer. You can read the license agreement at the following link: https://www.llama.com/llama3/license/.

## Ollama CLI

The Ollama CLI provides a range of commands to manage and run LLMs locally. While the exact commands and options may vary depending on the version of Ollama and its implementation, the following is a general overview of common CLI commands and their typical usage.

Let's explore what you can do with the Ollama CLI with the Llama 3 model:
1.	At the command prompt or terminal, enter the command to check its version, as shown in the following command:
```shell
ollama --version
```
2.	Note the response, as shown in the following output:
```
ollama version is 0.13.5
```
3.	At the command prompt or terminal, enter the command to pull down a named model like Meta’s Llama3 or Google’s Gemma3, as shown in the following command:
```shell
ollama pull gemma3:1b
```
> You can see the Llama3 models at the following link: https://ollama.com/library/llama3. You can see all the Gemma3 models at the following link: https://ollama.com/library/gemma3.

4.	Note the response, as shown in the following output:
```
pulling manifest
pulling 6a0746a1ec1a... 100% ▕████████████████████████████████████████████████████████▏ 4.7 GB
pulling 4fa551d4f938... 100% ▕████████████████████████████████████████████████████████▏  12 KB
pulling 8ab4849b038c... 100% ▕████████████████████████████████████████████████████████▏  254 B
pulling 577073ffcc6c... 100% ▕████████████████████████████████████████████████████████▏  110 B
pulling 3f8eb4da87fa... 100% ▕████████████████████████████████████████████████████████▏  485 B
verifying sha256 digest
writing manifest
success
```
> You can remove a model using the following command: `ollama rm gemma3:1b`.

5.	At the command prompt or terminal, enter the command to list the available local models, as shown in the following command:
```shell
ollama list
```
6.	Note the response, as shown in the following output:
```
NAME              ID              SIZE    MODIFIED
gemma3:1b         8648f39daa8f    815 MB  7 seconds ago
llama3.1:latest   46e0c10c039e    4.9 GB  2 minutes ago
```
7.	At the command prompt or terminal, enter the command to run a named model (which will also download it if not already pulled):
```shell
ollama run gemma3:1b
```
8.	Note the response, as shown in the following output:
```
>>> Send a message (/? for help)
```
9.	Enter a prompt, like, `What is Django?`, and note the response, which will use Markdown syntax for formatting.
10.	Enter the command to exit: `/bye`

>Ollama CLI also has commands to copy models and create new models. You can learn about these and other commands in the documentation at the following link: https://github.com/ollama/ollama#cli-reference.

Ollama provides client libraries for Python and JavaScript.

## Integrating Ollama with VS Code

Let’s do it:
1.	Add Ollama as a model provider in VS Code via the AI Toolkit extension, which can add Ollama models and endpoints into VS Code’s model list.

> You can learn more about the AI Toolkit extension at the following link: https://learn.microsoft.com/en-us/windows/ai/toolkit/

2.	A typical UI flow (as documented by Ollama) is in the Copilot sidebar. In the **Pick Model** dropdown, expand **Other Models**, choose **Manage Models**, pick **Ollama** as the provider, and select the local model you pulled, as shown in *Figure 1*:

![Selecting a model for GitHub Copilot in VS Code](local-models-01.png) 

*Figure 1: Selecting a model for GitHub Copilot in VS Code*

3.	In Copilot Chat, switch the model to your Ollama-backed model.
4.	Test with a safe task first: `Explain what this function does` or `Generate unit tests for this method signature`.

## Friends Don't Let Friends Use Ollama (April 15, 2026 by Zetaphor)

From `zetaphor`'s essay: 
*"Ollama gained traction by being the first easy llama.cpp wrapper, then spent years dodging attribution, misleading users, and pivoting to cloud, all while riding VC money earned on someone else's engine. Here's the full history, and why the alternatives are better."* https://sleepingrobots.com/dreams/stop-using-ollama/
