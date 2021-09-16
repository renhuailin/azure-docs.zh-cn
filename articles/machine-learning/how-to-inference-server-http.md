---
title: Azure 机器学习推理 HTTP 服务器
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习推理 HTTP 服务器实现本地开发。
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: inference server, local development, local debugging, devplatv2
ms.date: 05/14/2021
ms.openlocfilehash: 48dda26415113bff4ff20305ee8779804859be33
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428301"
---
# <a name="azure-machine-learning-inference-http-server-preview"></a>Azure 机器学习推理 HTTP 服务器（预览版）

Azure 机器学习推理 HTTP 服务器[（预览版）](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)是一个 Python 包，可用于在本地开发环境中轻松验证入口脚本 (`score.py`)。 如果评分脚本出现问题，该服务器将返回一个错误。 它还将返回发生错误的位置。

在持续集成和部署管道中创建验证入口时，也可以使用该服务器。 例如，使用候选脚本启动服务器，并针对本地终结点运行测试套件。

## <a name="prerequisites"></a>先决条件

- 需要：Python >=3.7

## <a name="installation"></a>安装

> [!NOTE]
> 若要避免包冲突，请在虚拟环境中安装服务器。

若要安装 `azureml-inference-server-http package`，请在 cmd/ 终端中运行以下命令：

```bash
python -m pip install azureml-inference-server-http
```

## <a name="use-the-server"></a>使用服务器

1. 创建目录以保存文件：

    ```bash
    mkdir server_quickstart
    cd server_quickstart
    ```

1. 若要避免包冲突，请创建一个虚拟环境并激活该环境：

    ```bash
    virtualenv myenv
    source myenv/bin/activate
    ```

1. 从 [pypi](https://pypi.org/project/azureml-inference-server-http/) 源安装 `azureml-inference-server-http` 包：

    ```bash
    python -m pip install azureml-inference-server-http
    ```

1. 创建入口脚本 (`score.py`)。 以下示例创建一个基本的入口脚本：

    ```bash
    echo '
    import time

    def init():
        time.sleep(1)

    def run(input_data):
        return {"message":"Hello, World!"}
    ' > score.py
    ```

1. 启动服务器并将 `score.py` 设置为入口脚本：

    ```bash
    azmlinfsrv --entry_script score.py
    ```

    > [!NOTE]
    > 服务器托管在 0.0.0.0 上，这意味着它将侦听托管计算机的所有 IP 地址。

1. 使用 `curl` 将评分请求发送到服务器：

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

    服务器的响应方式应如下所示。

    ```bash
    {"message": "Hello, World!"}
    ```

现在，可以通过再次运行服务器来修改评分脚本并测试更改。

## <a name="server-routes"></a>服务器路由

服务器正在侦听这些路由的端口 5001。

| 名称 | 路由|
| --- | --- |
| 运行情况探测 | 127.0.0.1:5001/|
| 分数 | 127.0.0.1:5001/score|

## <a name="server-parameters"></a>服务器参数

下表包含服务器接受的参数：

| 参数 | 必须 | 默认 | 说明 |
| ---- | --- | ---- | ----|
| entry_script | True | 空值 | 评分脚本的相对路径或绝对路径。|
| model_dir | 错误 | 空值 | 包含用于推理的模型的目录的相对路径或绝对路径。
| port | False | 5001 | 服务器的服务端口。|
| worker_count | False | 1 | 将处理并发请求的工作线程数。 |
| appinsights_instrumentation_key | 错误 | 空值 | 将发布日志的 Application Insights 的检测密钥。 |

## <a name="request-flow"></a>请求流

以下步骤说明了 Azure 机器学习推理 HTTP 服务器如何处理传入请求：

1. Python CLI 包装器位于服务器的网络堆栈周围，用于启动服务器。
1. 客户端向服务器发送请求。
1. 收到请求时，该请求会通过 [WSGI](https://www.fullstackpython.com/wsgi-servers.html) 服务器，然后被分派给某个辅助角色。
    - 在 Linux 上使用 [Gunicorn](https://docs.gunicorn.org/)。
    - 在 Windows 上使用 [Waitress](https://docs.pylonsproject.org/projects/waitress/)。
1. 然后，[Flask](https://flask.palletsprojects.com/) 应用会处理请求，并加载入口脚本和任何依赖项。
1. 最后，将请求发送到入口脚本。 然后，入口脚本对已加载的模型进行推理调用并返回响应。

:::image type="content" source="./media/how-to-inference-server-http/inference-server-architecture.png" alt-text="HTTP 服务器进程示意图":::

## <a name="how-to-integrate-with-visual-studio-code"></a>如何与 Visual Studio Code 集成

可通过两种方式使用 Visual Studio Code (VSCode) 和 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)通过 [azureml-inference-server-http](https://pypi.org/project/azureml-inference-server-http/) 包进行调试。 

1. 用户在命令行中启动 AzureML 推理服务器，并使用 VSCode + Python 扩展附加到进程。
1. 用户在 VSCode 中设置 `launch.json`，并在 VSCode 中启动 AzureML 推理服务器。

通过这两种方式，用户可以逐步设置断点和调试。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="do-i-need-to-reload-the-server-when-changing-the-score-script"></a>更改评分脚本时，是否需要重新加载服务器？

更改评分脚本 (`score.py`) 后，按 `ctrl + c` 停止服务器。 然后运行 `azmlinfsrv --entry_script score.py` 重新启动它。

### <a name="which-os-is-supported"></a>支持哪种操作系统？

Azure 机器学习推理服务器在基于 Windows 和 Linux 的操作系统上运行。

## <a name="next-steps"></a>后续步骤

* 有关如何创建入口脚本和部署模型的详细信息，请参阅[如何使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。
* 了解[用于推理的预生成 Docker 映像](concept-prebuilt-docker-images-inference.md)