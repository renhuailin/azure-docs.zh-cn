---
title: 排查预生成 Docker 映像的问题
titleSuffix: Azure Machine Learning
description: 排查使用用于推理的预生成 Docker 映像时出现的问题的步骤。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, troubleshoot
ms.openlocfilehash: c4949000cee07e2280d9edc018ff07f20f37a352
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427653"
---
# <a name="troubleshooting-prebuilt-docker-images-for-inference-preview"></a>用于推理的预生成 Docker 映像（预览版）故障排除

了解如何排查在 Azure 机器学习中使用用于推理的预生成 Docker 映像[（预览版）](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)时可能出现的问题。

## <a name="model-deployment-failed"></a>模型部署失败

如果模型部署失败，你将不会在 [Azure 机器学习工作室](https://ml.azure.com/)中看到日志，并且 `service.get_logs()` 将返回 None。
如果 score.py 的 init() 函数存在问题，`service.get_logs()` 将返回有关此问题的日志。

因此，需要使用下面所示的命令之一在本地运行容器，并将 `<MCR-path>` 替换为映像路径。 有关映像和路径的列表，请参阅[用于推理的预生成 Docker 映像](concept-prebuilt-docker-images-inference.md)。

### <a name="mounting-extensibility-solution"></a>装载扩展性解决方案

转到包含 `score.py` 的目录，并运行：

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_PYTHON_LIB_PATH="myenv/lib/python3.7/site-packages" <mcr-path>
```

### <a name="requirementstxt-extensibility-solution"></a>requirements.txt 扩展性解决方案

转到包含 `score.py` 的目录，并运行：

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_REQUIREMENTS_TXT="requirements.txt" <mcr-path>
```

## <a name="enable-local-debugging"></a>启用本地调试

在本地推理服务器中可以快速调试入口脚本 (`score.py`)。 如果基础评分脚本存在 bug，该服务器将无法初始化或者为模型提供服务。 相反，它会引发异常并指出发生问题的位置。 [详细了解 Azure 机器学习推理 HTTP 服务器](how-to-inference-server-http.md)

## <a name="for-common-model-deployment-issues"></a>对于常见模型部署问题

对于将模型从 Azure 机器学习部署到 Azure 容器实例 (ACI) 或 Azure Kubernetes 服务 (AKS) 时出现的问题，请参阅[排查模型部署问题](how-to-troubleshoot-deployment.md)。

## <a name="init-or-run-failing-to-write-a-file"></a>init() 或 run() 无法写入文件

预生成 Docker 映像中的 HTTP 服务器是以非 root 用户身份运行的，它不一定对所有目录都拥有访问权限。 请仅写入到你有权访问的目录。 例如，容器中的 `/tmp` 目录。

## <a name="extra-python-packages-not-installed"></a>不安装额外的 Python 包

* 检查环境变量或文件名是否存在拼写错误。
* 检查容器日志以查看是否已安装 `pip install -r <your_requirements.txt>`。
* 检查是否在[推理配置](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)构造函数中正确设置了源目录。
* 如果找不到安装并且日志指出“找不到文件”，请检查日志中显示的文件名是否正确。
* 如果安装已启动但失败或者超时，请尝试在干净的环境中本地安装具有相同 Python 和 pip 版本的同一个 `requirements.txt`（即，不指定缓存目录；`pip install --no-cache-dir -r requriements.txt`）。 确定是否可以在本地重现该问题。

## <a name="mounting-solution-failed"></a>装载解决方案失败

* 检查环境变量或目录名是否存在拼写错误。
* 环境变量必须设置为 `score.py` 文件的相对路径。
* 检查是否在[推理配置](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)构造函数中正确设置了源目录。
* 该目录需是环境的“site-packages”目录。
* 如果 `score.py` 仍返回 `ModuleNotFound`，并且模块应该是在装载的目录中，请尝试输出 `init()` 或 `run()` 中的 `sys.path`，以查看是否缺少任何路径。

## <a name="building-an-image-based-on-the-prebuilt-docker-image-failed"></a>基于预生成的 Docker 映像生成映像失败

* 如果在安装 apt 包过程中失败，请检查在运行 apt 命令之前是否已将用户设置为 root。 （确保切换回到非 root 用户） 

## <a name="image-built-based-on-the-prebuilt-docker-image-cant-boot-up"></a>基于预生成的 Docker 映像生成的映像无法启动

* 非 root 用户需是 `dockeruser`。 否则，必须将以下目录的所有者设置为运行映像时要使用的用户名：

    ```bash
    /var/runit
    /var/log
    /var/lib/nginx
    /run
    /opt/miniconda
    /var/azureml-app
    ```

* 如果 `ENTRYPOINT` 在新生成的映像中已更改，则需要通过 `runsvdir /var/runit` 加载 HTTP 服务器和相关组件

## <a name="next-steps"></a>后续步骤

* [将 Python 包添加到预生成的映像](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [使用预生成的包作为新 Dockerfile 的基础](how-to-extend-prebuilt-docker-image-inference.md)。