**How can a .NET developer study AI?**

A .NET developer can get into AI while leveraging their existing skills and tooling. You don’t need to switch to Python entirely (though it helps to understand it because so many AI resources use it), and there are .NET-native and interop-friendly options. 

But by limiting yourself to .NET, you're trying to learn AI with one hand tied behind your back. If you're serious about AI, then let go of the idea you can do it only with .NET.

Here’s a possible path broken down into skills, tools, and projects, starting from foundational knowledge to more advanced, real-world applications.

- [Understand the Fundamentals of AI \& ML](#understand-the-fundamentals-of-ai--ml)
  - [Recommended Free Resources](#recommended-free-resources)
- [.NET-Compatible Tools for AI](#net-compatible-tools-for-ai)
  - [Microsoft Learn - AI apps for .NET developers](#microsoft-learn---ai-apps-for-net-developers)
  - [ML.NET](#mlnet)
  - [ONNX Runtime + .NET](#onnx-runtime--net)
  - [TorchSharp](#torchsharp)
- [Python and Interop](#python-and-interop)
- [Developer Tools and Workflow](#developer-tools-and-workflow)
- [Structured Roadmap](#structured-roadmap)
  - [Beginner](#beginner)
  - [Intermediate](#intermediate)
  - [Advanced](#advanced)
- [Projects to Build as a .NET Developer](#projects-to-build-as-a-net-developer)
  - [ML.NET Projects](#mlnet-projects)
  - [ONNX + .NET](#onnx--net)
  - [AI-as-a-Service (Python backend)](#ai-as-a-service-python-backend)
- [Cloud AI Services in .NET](#cloud-ai-services-in-net)
  - [Microsoft Azure Cognitive Services](#microsoft-azure-cognitive-services)
  - [Azure OpenAI Service](#azure-openai-service)
- [Summary](#summary)

 # Understand the Fundamentals of AI & ML

Even as a .NET developer, if you want to get serious about AI then you need to know the theory:
- Machine Learning – supervised vs unsupervised learning, classification, regression, clustering.
- Deep Learning – neural networks, CNNs, RNNs, transformers.
- NLP, Computer Vision, Reinforcement Learning, etc.

## Recommended Free Resources
- [Google’s Machine Learning Crash Course](https://developers.google.com/machine-learning/crash-course)
- [Fast.ai](https://www.fast.ai/)
- [Microsoft Learn - Develop AI Solutions with Azure AI Services](https://learn.microsoft.com/en-us/plans/ai/)
- [Andrew Ng’s Machine Learning Course (Coursera)](https://www.coursera.org/learn/machine-learning)

> Don’t get paralyzed by math at first. Learn the concepts, then revisit the linear algebra and stats as needed.

# .NET-Compatible Tools for AI

## Microsoft Learn - AI apps for .NET developers

Learn to build AI apps with .NET. Browse sample code, tutorials, quickstarts, conceptual articles, and more.

https://learn.microsoft.com/en-us/dotnet/ai/

## ML.NET

This is Microsoft's official machine learning framework for .NET.
- Use case: Sentiment analysis, recommendation systems, image classification, regression, clustering.
- Tooling: Works with C# in Visual Studio, has a model builder UI, or can be scripted.
- Integration: Easy deployment into ASP.NET Core apps, desktop apps, or Azure Functions.

I used to have a chapter about ML.NET in the 4th and 5th editions of my c# and .NET fundamentals book, but no reader ever asked about that chapter, or gave any feedback, so I removed it to make space for other topics and no one complained. I suspect very few .NET developers ever touch ML.NET. 

> ML.NET documentation: https://learn.microsoft.com/en-us/dotnet/machine-learning/

## ONNX Runtime + .NET

If you're using pretrained models (e.g., from PyTorch or TensorFlow), you can export them to ONNX format and run them in .NET.
- Use case: Use high-end AI models (e.g., OpenAI Whisper, BERT) without writing Python.
- Tool: `Microsoft.ML.OnnxRuntime`

## TorchSharp

A .NET binding for LibTorch (PyTorch). Hardcore but powerful.
- [GitHub - TorchSharp](https://github.com/dotnet/TorchSharp)

> ML.NET is great for getting started, but for cutting-edge models, you’ll eventually touch ONNX or interop with Python.

# Python and Interop

Even if you stay in .NET, Python is dominant in AI:
- Read notebooks
- Convert models
- Run training jobs

Learn:
- `pandas`, `numpy`, `matplotlib`
- `scikit-learn`
- `transformers` (HuggingFace)
- `torch` or `tensorflow`

For interop:
- Run Python from .NET: via Pythonnet: https://github.com/pythonnet/pythonnet
- REST APIs: Run AI in Python, call from .NET (good separation)

# Developer Tools and Workflow

- Visual Studio or Rider: Great for ML.NET or ONNX
- Jupyter Notebooks (via Polyglot Notebooks with .NET Interactive): Write C# notebooks, experiment with ML models
- Docker: Useful for deploying AI microservices in Python or .NET
- Azure Machine Learning Studio: Drag-and-drop model builder + Python/ML.NET integration

# Structured Roadmap

## Beginner

- ML.NET basics: regression, classification, clustering
- Train a model with Visual Studio ML.NET Model Builder
- Make predictions in a console app
- Basic Python for AI

## Intermediate

- Load ONNX models in .NET
- Train a model in Python and use in .NET
- Serve Python AI with FastAPI, call from .NET (microservice architecture)
- Integrate ML into ASP.NET Core or desktop UI

## Advanced

- Use TorchSharp for custom models
- Azure AI (Vision, OpenAI, Speech) in .NET
- Finetune transformer models in Python, run inference in .NET
- Multi-modal AI (text + image), multi-threaded inference in .NET

# Projects to Build as a .NET Developer

## ML.NET Projects

- Sales forecasting using regression
- Product recommendation engine
- Text classification for customer emails

## ONNX + .NET

- Use a BERT model for document tagging
- Use Whisper for speech-to-text transcription in a WPF app

## AI-as-a-Service (Python backend)

- Image classification using HuggingFace + FastAPI
- Chatbot service using OpenAI + LangChain, with .NET frontend

# Cloud AI Services in .NET

## Microsoft Azure Cognitive Services

- Vision, Speech, Language APIs
- Easy .NET SDKs
- Can integrate into existing .NET apps quickly

## Azure OpenAI Service

- Use GPT-4, DALL·E, etc.
- Azure OpenAI .NET SDK

# Summary

Even as a C# developer, embrace a multi-language mindset. Build your AI brain in Python, but bring the product to life in .NET. That’s the real-world workflow most teams are using right now.

> I plan to write a book about Python next year, hopefully to publish in Summer 2026, after I have published .NET 10 editions of my quartet of .NET books that are coming this winter.
