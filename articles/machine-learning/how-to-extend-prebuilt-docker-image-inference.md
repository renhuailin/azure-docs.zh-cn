---
title: 扩展预生成的 Docker 映像
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习中扩展预生成的 Docker 映像
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: c4a1b52f87f0c5053e357fcb67a2e90962dc37c1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433523"
---
# <a name="extend-a-prebuilt-docker-image-preview"></a>扩展预生成的 Docker 映像（预览版）

在某些情况下，Azure 机器学习的[用于模型推理的预生成 Docker 映像](concept-prebuilt-docker-images-inference.md)和[扩展性](./how-to-prebuilt-docker-images-inference-python-extensibility.md)解决方案可能不满足你的推理服务需求。

在这种情况下，可以使用 Dockerfile 创建新映像，并将某个预生成的映像用作起点。 通过从现有的预生成 Docker 映像进行扩展，可以使用 Azure 机器学习网络堆栈和库，而无需从头开始创建映像。

> [!IMPORTANT]
> 将预生成的 Docker 映像与 Azure 机器学习配合使用的功能目前以预览版提供。 预览功能按原样提供，不保证支持或服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

**优点和缺点**

使用 Dockerfile 可以在部署之前对映像进行完全自定义。 这样可以最大程度地控制要在容器中设置哪些依赖项或环境变量。

此方法的主要缺点是在部署过程中需要进行额外的映像生成，这会减缓部署过程。 如果可以使用 [Python 包扩展性](./how-to-prebuilt-docker-images-inference-python-extensibility.md)方法，则部署速度将更快。
## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关创建工作区的教程，请参阅 [Azure 机器学习入门](quickstart-create-resources.md)。
* 熟悉如何创作 [Dockerfile](https://docs.docker.com/engine/reference/builder/)。
* 一个正常工作的 [Docker](https://www.docker.com/) 本地安装（包括 `docker` CLI），或者与你的 Azure 机器学习工作区关联的 Azure 容器注册表 (ACR)。

    > [!WARNING]
    > 首次使用工作区训练或部署模型时，将创建工作区的 Azure 容器注册表。 如果你创建了一个新的工作区，但没有训练或创建模型，则该工作区将不存在 Azure 容器注册表。
## <a name="create-and-build-dockerfile"></a>创建和生成 Dockerfile

下面是一个示例 Dockerfile，它使用 Azure 机器学习预生成的 Docker 映像作为基础映像：

```Dockerfile
FROM mcr.microsoft.com/azureml/<image_name>:<tag>

COPY requirements.txt /tmp/requirements.txt

RUN pip install –r /tmp/requirements.txt
```

然后，将上述 Dockerfile 放入包含所有所需文件的目录，并运行以下命令来生成映像：

```bash
docker build -f <above dockerfile> -t <image_name>:<tag> .
```

> [!TIP]
> 在此 [Docker 文档](https://docs.docker.com/engine/reference/commandline/build/)中可以找到有关 `docker build` 的更多详细信息。

如果 `docker build` 命令在本地不可用，请使用适用于你的 Azure 机器学习工作区的 Azure 容器注册表 ACR 在云中生成 Docker 映像。 有关详细信息，请参阅[教程：使用 Azure 容器注册表生成和部署容器映像](../container-registry/container-registry-tutorial-quick-task.md)。

> [!IMPORTANT]
> Microsoft 建议先验证你的 Dockerfile 是否可在本地工作，然后再尝试通过 Azure 容器注册表创建自定义的基础映像。

以下部分包含有关 Dockerfile 的更具体详细信息。

## <a name="install-extra-packages"></a>安装额外的包

如果需要在 Ubuntu 容器中安装任何其他 `apt` 包，可将这些包添加到 Dockerfile 中。 以下示例演示如何通过 Dockerfile 使用 `apt-get` 命令：

```Dockerfile
FROM <prebuilt docker image from MCR>

# Switch to root to install apt packages
USER root:root

RUN apt-get update && \
    apt-get install -y \
    <package-1> \
    ... 
    <package-n> && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser
```

还可以通过 Dockerfile 安装其他 pip 包。 以下示例演示如何使用 `pip install`：

```Dockerfile
RUN pip install <library>
```

<a id="buildmodel"></a>

## <a name="build-model-and-code-into-images"></a>将模型和代码生成到映像中

如果需要将模型和代码生成到映像中，需要在 Dockerfile 中设置以下环境变量：

* `AZUREML_ENTRY_SCRIPT`：代码的入口脚本。 此文件包含 `init()` 和 `run()` 方法。
* `AZUREML_MODEL_DIR`：包含模型文件的目录。 入口脚本应使用此目录作为模型的根目录。

以下示例演示如何在 Dockerfile 中设置这些环境变量：

```Dockerfile
FROM <prebuilt docker image from MCR>

# Code
COPY <local_code_directory> /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=<entryscript_file_name>

# Model
COPY <model_directory> /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="example-dockerfile"></a>示例 Dockerfile

以下示例演示如何安装 `apt` 包、设置环境变量，并将代码和模型包含为 Dockerfile 的一部分：

```Dockerfile
FROM mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest 

USER root:root

# Install libpng-tools and opencv
RUN apt-get update && \
    apt-get install -y \
    libpng-tools \
    python3-opencv && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser

# Code
COPY code /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=score.py

# Model
COPY model /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="next-steps"></a>后续步骤

若要将 Dockerfile 与 Azure 机器学习 Python SDK 配合使用，请参阅以下文档：

* [使用自己的本地 Dockerfile](how-to-use-environments.md#use-your-own-dockerfile)
* [使用预生成的 Docker 映像并创建自定义基础映像](how-to-use-environments.md#use-a-prebuilt-docker-image)

若要详细了解如何部署模型，请参阅[如何部署模型](how-to-deploy-and-where.md)。

若要了解如何排查预生成 Docker 映像部署的问题，请参阅[如何排查预生成 Docker 映像部署的问题](how-to-troubleshoot-prebuilt-docker-image-inference.md)。