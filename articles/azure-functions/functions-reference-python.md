---
title: Azure Functions Python 开发人员参考
description: 了解如何使用 Pythong 开发函数
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: aa48731248c9e51d680bc0e1b396115c54edbcd7
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260846"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 开发人员指南

本文介绍了如何使用 Python 开发 Azure Functions。 以下内容假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。

作为 Python 开发人员，你可能还会对下列某篇文章感兴趣：

| 入门 | 概念| 场景/示例 |
|--|--|--|
| <ul><li>[使用 Visual Studio Code 的 Python 函数](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)</li><li>[使用终端/命令提示符的 Python 函数](./create-first-function-cli-csharp.md?pivots=programming-language-python)</li></ul> | <ul><li>[开发人员指南](functions-reference.md)</li><li>[托管选项](functions-scale.md)</li><li>[性能&nbsp;注意事项](functions-best-practices.md)</li></ul> | <ul><li>[图像分类与 PyTorch](machine-learning-pytorch.md)</li><li>[Azure 自动化示例](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[将机器学习与 TensorFlow 配合使用](functions-machine-learning-tensorflow.md)</li><li>[浏览 Python 示例](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> 尽管可以[在 Windows 上本地开发基于 Python 的 Azure Functions](create-first-function-vs-code-python.md#run-the-function-locally)，但仅在 Azure 中运行时，基于 Linux 的托管计划才支持 Python。 请参阅支持的[操作系统/运行时](functions-scale.md#operating-systemruntime)组合列表。

## <a name="programming-model"></a>编程模型

Azure Functions 要求函数是 Python 脚本中处理输入并生成输出的无状态方法。 默认情况下，运行时期望该方法在 `__init__.py` 文件中作为名为 `main()` 的全局方法实现。 还可以[指定备用入口点](#alternate-entry-point)。

来自触发器和绑定的数据使用在 function.json 文件中定义的 `name` 属性，通过方法特性绑定到函数。 例如，下面的 function.json 描述一个由名为 `req` 的 HTTP 请求触发的简单函数：

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

根据这一定义，包含函数代码的 `__init__.py` 文件可能类似于以下示例：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

还可以使用 Python 类型注释在函数中显式声明属性类型和返回类型。 这有助于使用许多 Python 代码编辑器提供的 intellisense 和自动完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

使用 [ azure.functions.*](/python/api/azure-functions/azure.functions) 包中附带的 Python 注释将输入和输出绑定到方法。

## <a name="alternate-entry-point"></a>备用入口点

可以选择在 function.json 文件中指定 `scriptFile` 和 `entryPoint` 属性来更改函数的默认行为。 例如，下面的 function.json 指示运行时使用 main.py 文件中的 `customentry()` 方法作为 Azure 函数的入口点 。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>文件夹结构

Python 函数项目的建议文件夹结构如以下示例所示：

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
主项目文件夹 (<project_root>) 可以包含以下文件：

* *local.settings.json*：用于在本地运行时存储应用设置和连接字符串。 此文件不会被发布到 Azure。 若要了解详细信息，请参阅 [local.settings.file](functions-develop-local.md#local-settings-file)。
* requirements.txt：包含在发布到 Azure 时系统安装的 Python 包列表。
* *host.json*：包含在函数应用中影响所有函数的全局配置选项。 此文件会被发布到 Azure。 本地运行时，并非所有选项都受支持。 若要了解详细信息，请参阅 [host.json](functions-host-json.md)。
* .vscode/：（可选）包含存储 VSCode 配置。 若要了解详细信息，请参阅 [VSCode 设置](https://code.visualstudio.com/docs/getstarted/settings)。
* .venv/：（可选）包含本地开发使用的 Python 虚拟环境。
* Dockerfile：（可选）在[自定义容器](functions-create-function-linux-custom-image.md)中发布项目时使用。
* tests/：（可选）包含函数应用的测试用例。
* .funcignore：（可选）声明不应发布到 Azure 的文件。 通常，此文件包含 `.vscode/` 以忽略编辑器设置，包含 `.venv/` 以忽略本地 Python 虚拟环境，包含 `tests/` 以忽略测试用例，包含 `local.settings.json` 以阻止发布本地应用设置。

每个函数都有自己的代码文件和绑定配置文件 (function.json)。

在 Azure 中将项目部署到函数应用时，主项目 (<project_root>) 文件夹的整个内容应包含在包中，但不包含该文件夹本身，这意味着 `host.json` 应位于包根目录中。 建议你在一个文件夹中维护测试以及其他函数，在此示例中为 `tests/`。 有关详细信息，请参阅[单元测试](#unit-testing)。

## <a name="import-behavior"></a>导入行为

可以使用绝对引用和相对引用在函数代码中导入模块。 根据以上所示的文件夹结构，以下导入在函数文件 *<project_root>\my\_first\_function\\_\_init\_\_.py* 中工作：

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  使用绝对导入语法时，shared_code/ 文件夹需要包含 \_\_init\_\_.py 文件以将其标记为 Python 包。

以下 \_app\_\_\_ 导入和 beyond top-level 相对导入已弃用，因为不受静态类型检查器支持，且不受 Python 测试框架支持：

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>触发器和输入

在 Azure Functions 中，输入分为两种类别：触发器输入和其他输入。 虽然它们在 `function.json` 文件中并不相同，但它们在 Python 代码中的使用方法却是相同的。  在本地运行时，触发器和输入源的连接字符串或机密映射到 `local.settings.json` 文件中的值，而在 Azure 中运行时则映射到应用程序设置。

例如，以下代码演示两者之间的差异：

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

调用函数时，HTTP 请求作为 `req` 传递给函数。 将基于路由 URL 中的 ID 从 Azure Blob 存储检索一个条目，并在函数体中用作 `obj`。  此处指定的存储帐户是在 AzureWebJobsStorage 应用设置中找到的连接字符串，该帐户即函数应用使用的同一个存储帐户。


## <a name="outputs"></a>Outputs

输出可以在返回值和输出参数中进行表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

若要使用函数的返回值作为输出绑定的值，则绑定的 `name` 属性应在 `function.json` 中设置为 `$return`。

若要生成多个输出，请使用 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) 接口提供的 `set()` 方法将值分配给绑定。 例如，以下函数可以将消息推送到队列，还可返回 HTTP 响应。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>日志记录

可在函数应用中通过根 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 处理程序来访问 Azure Functions 运行时记录器。 此记录器绑定到 Application Insights，并允许标记在函数执行期间遇到的警告和错误。

下面的示例在通过 HTTP 触发器调用函数时记录信息消息。

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

有更多日志记录方法可用于在不同跟踪级别向控制台进行写入：

| 方法                 | 说明                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 在根记录器中写入具有 CRITICAL 级别的消息。  |
| **`error(_message_)`**   | 在根记录器中写入具有 ERROR 级别的消息。    |
| **`warning(_message_)`**    | 在根记录器中写入具有 WARNING 级别的消息。  |
| **`info(_message_)`**    | 在根记录器中写入具有 INFO 级别的消息。  |
| **`debug(_message_)`** | 在根记录器中写入具有 DEBUG 级别的消息。  |

若要详细了解日志记录，请参阅[监视 Azure Functions](functions-monitoring.md)。

### <a name="log-custom-telemetry"></a>记录自定义遥测数据

默认通过 Functions 运行时为 Functions 应用收集日志遥测。 此遥测最终会作为 Application Insights 中的跟踪。 默认情况下，某些 Azure 服务的请求和依赖项遥测还通过[函数绑定](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings?tabs=csharp#supported-bindings)进行收集。 若要收集自定义请求/依赖项遥测（不通过绑定），可以使用 [OpenCensus Python 扩展](https://github.com/census-ecosystem/opencensus-python-extensions-azure)将自定义遥测数据发送到 Application Insights 实例。

可以在[此处](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib)找到支持的库的列表。

>[!NOTE]
> 若要使用 OpenCensus Python 扩展，需要在 `local.settings.json` 和应用程序设置中将 `PYTHON_ENABLE_WORKER_EXTENSIONS` 设置为 `1` 来启用 [Python 扩展](#python-worker-extensions)
>

```
// requirements.txt
...
opencensus-extension-azure-functions
opencensus-ext-requests
```

```python
import json
import logging

import requests
from opencensus.extension.azure.functions import OpenCensusExtension
from opencensus.trace import config_integration

config_integration.trace_integrations(['requests'])

OpenCensusExtension.configure()

def main(req, context):
    logging.info('Executing HttpTrigger with OpenCensus extension')

    # You must use context.tracer to create spans
    with context.tracer.span("parent"):
        response = requests.get(url='http://example.com')

    return json.dumps({
        'method': req.method,
        'response': response.status_code,
        'ctx_func_name': context.function_name,
        'ctx_func_dir': context.function_directory,
        'ctx_invocation_id': context.invocation_id,
        'ctx_trace_context_Traceparent': context.trace_context.Traceparent,
        'ctx_trace_context_Tracestate': context.trace_context.Tracestate,
    })
```

## <a name="http-trigger-and-bindings"></a>HTTP 触发器和绑定

HTTP 触发器在 function.json 文件中定义。 绑定的 `name` 必须与函数中的命名参数匹配。
前面的示例中使用了绑定名称 `req`。 此参数是 [HttpRequest] 对象，并返回 [HttpResponse] 对象。

从 [HttpRequest] 对象中，可以获取请求标头、查询参数、路由参数和消息正文。

以下示例来自[适用于 Python 的 HTTP 触发器模版](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

在此函数中，`name` 查询参数的值从 [HttpRequest] 对象的 `params` 参数获取。 JSON 编码的消息正文使用 `get_json` 方法来读取。

同样，你可以在返回的 [HttpResponse] 对象中为响应消息设置 `status_code` 和 `headers`。

## <a name="scaling-and-performance"></a>缩放和性能

有关 Python 函数应用的缩放和性能最佳做法，请参阅 [Python 缩放和性能](python-scale-performance-reference.md)一文。

## <a name="context"></a>上下文

若要在执行过程中获取函数的调用上下文，请在其签名中包含 [`context`](/python/api/azure-functions/azure.functions.context) 参数。

例如：

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[Context](/python/api/azure-functions/azure.functions.context) 类具有以下字符串属性：

`function_directory` 在其中运行函数的目录。

`function_name` 函数的名称。

`invocation_id` 当前函数调用的 ID。

## <a name="global-variables"></a>全局变量

应用的状态是否将保留，以供将来执行使用，这一点不能保证。 但是，Azure Functions 运行时通常会为同一应用的多次执行重复使用同一进程。 为缓存成本昂贵的计算结果，请将其声明为全局变量。

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用程序设置](functions-app-settings.md)在执行过程中将公开为环境变量。 在代码中访问这些设置有两种主要方法。 

| 方法 | 说明 |
| --- | --- |
| **`os.environ["myAppSetting"]`** | 尝试通过键名获取应用程序设置，失败时引发错误。  |
| **`os.getenv("myAppSetting")`** | 尝试通过键名获取应用程序设置，失败时返回 NULL。  |

这两种方法都需要声明 `import os`。

以下示例使用名为 `myAppSetting` 的键通过 `os.environ["myAppSetting"]` 获取[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

对于本地开发，应用程序设置[在 local.settings.json 文件中维护](functions-develop-local.md#local-settings-file)。

## <a name="python-version"></a>Python 版本

Azure Functions 支持以下 Python 版本：

| Functions 版本 | Python<sup>*</sup> 版本 |
| ----- | ----- |
| 3.x | 3.9（预览版） <br/> 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>正式 CPython 分发

若要在 Azure 中创建函数应用时请求特定的 Python 版本，请使用 [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create) 命令的 `--runtime-version` 选项。 函数运行时版本由 `--functions-version` 选项设置。 Python 版本在创建函数应用时设置，并且无法更改。

在本地运行时，运行时使用可用的 Python 版本。

### <a name="changing-python-version"></a>更改 Python 版本

若要将 Python 函数应用设置为特定的语言版本，需要在站点配置的 `LinuxFxVersion` 字段中指定语言以及语言版本。例如，要将 Python 应用更改为使用 Python 3.8，需要将 `linuxFxVersion` 设置为 `python|3.8`。

若要详细了解 Azure Functions 运行时支持策略，请参阅[本文](./language-support-policy.md)

若要查看受支持的 Python 版本函数应用的完整列表，请参阅[本文](./supported-languages.md)



# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-linux)

可以通过 Azure CLI 查看和设置 `linuxFxVersion`。  

使用 Azure CLI，使用 [az functionapp config show](/cli/azure/functionapp/config) 命令查看当前 `linuxFxVersion`。

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

在此代码中，用函数应用名称替代 `<function_app>`。 此外，还使用函数应用的资源组名称替代 `<my_resource_group>`。 

在以下输出中，你会看到 `linuxFxVersion`，为了清晰起见，该输出已被截断：

```output
{
  ...
  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

可以使用 [az functionapp config set](/cli/azure/functionapp/config) 命令更新函数应用中的 `linuxFxVersion` 设置。

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

将 `<FUNCTION_APP>` 替换为你的函数应用的名称。 此外，还使用函数应用的资源组名称替代 `<RESOURCE_GROUP>`。 另外，将 `<LINUX_FX_VERSION>` 替换为要使用的 python 版本，前缀为 `python|`，例如 `python|3.9`

可以通过在前面代码示例中选择“试一试”运行这个来自 [Azure Cloud Shell](../cloud-shell/overview.md) 的命令。 还可以在执行 [az login](/cli/azure/reference-index#az-login) 登录后使用 [Azure CLI 在本地](/cli/azure/install-azure-cli)执行此命令。

在对站点配置进行更改后，函数应用会重启。

--- 


## <a name="package-management"></a>包管理

在使用 Azure Functions Core Tools 或 Visual Studio Code 进行本地开发时，将所需包的名称和版本添加到 `requirements.txt` 文件并使用 `pip` 安装它们。

例如，可以使用以下要求文件和 pip 命令从 PyPI 安装 `requests` 包。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>发布到 Azure

准备好进行发布时，确保所有公开发布的依赖项都在 requirements.txt 文件（位于项目目录的根目录）中列出。

发布中排除的项目文件和文件夹（包括虚拟环境文件夹）列在 .funcignore 文件中。

将 Python 项目发布到 Azure 时，支持三种生成操作：远程生成、本地生成以及使用自定义依赖项生成。

还可使用 Azure Pipelines 生成依赖项并使用持续交付 (CD) 发布。 若要了解详细信息，请参阅[使用 Azure DevOps 持续交付](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>远程生成

使用远程生成时，服务器上还原的依赖项和本机依赖项与生产环境匹配。 这导致要上传的部署包较小。 在 Windows 上开发 Python 应用时使用远程生成。 如果你的项目具有自定义依赖项，可[使用具有额外索引 URL 的远程生成](#remote-build-with-extra-index-url)。

依赖项根据 requirements.txt 文件的内容远程获取。 [远程生成](functions-deployment-technologies.md#remote-build)是推荐的生成方法。 默认情况下，使用下面的 [`func azure functionapp publish`](functions-run-local.md#publish) 命令将 Python 项目发布到 Azure 时，Azure Functions Core Tools 会请求远程生成。

```bash
func azure functionapp publish <APP_NAME>
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

默认情况下，[适用于 Visual Studio Code 的 Azure Functions 扩展](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)还会请求远程生成。

### <a name="local-build"></a>本地生成

依赖项根据 requirements.txt 文件的内容在本地获取。 可以使用下面的 [`func azure functionapp publish`](functions-run-local.md#publish) 命令发布本地生成，从而防止执行远程生成。

```command
func azure functionapp publish <APP_NAME> --build local
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

使用 `--build local` 选项，从 requirements.txt 文件中读取项目依赖项，同时在本地下载并安装这些依赖包。 项目文件和依赖项从本地计算机部署到 Azure。 这会将较大的部署包上传到 Azure。 如果由于某种原因，Core Tools 无法获取 requirements.txt 文件中的依赖项，则必须使用自定义依赖项选项进行发布。

在 Windows 上进行本地开发时，不建议使用本地生成。

### <a name="custom-dependencies"></a>自定义依赖项

如果项目的依赖项在 [Python 包索引](https://pypi.org/)中找不到，可通过两种方式生成该项目。 生成方法取决于你生成项目的方式。

#### <a name="remote-build-with-extra-index-url"></a>具有额外索引 URL 的远程生成

如果包可以从可访问的自定义包索引中获取，请使用远程生成。 在发布之前，请务必[创建一个](functions-how-to-use-azure-function-app-settings.md#settings) 名为 `PIP_EXTRA_INDEX_URL` 的应用设置。 此设置的值是自定义包索引的 URL。 使用此设置可告诉远程生成使用 `--extra-index-url` 选项运行 `pip install`。 若要了解详细信息，请参阅 [Python pip install 文档](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format)。

还可将基本身份验证凭据与额外的包索引 URL 结合使用。 若要了解详细信息，请参阅 Python 文档中的[基本身份验证凭据](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials)。

#### <a name="install-local-packages"></a>安装本地包

如果你的项目使用未向我们的工具公开发布的包，则可以通过将包放在 \_\_app\_\_/.python_packages 目录中，使其可供应用使用。 发布之前，运行以下命令以在本地安装依赖项：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自定义依赖项时，应使用 `--no-build` 发布选项，因为你已将依赖项安装到项目文件夹中。

```command
func azure functionapp publish <APP_NAME> --no-build
```

请记住将 `<APP_NAME>` 替换为 Azure 中的函数应用名称。

## <a name="unit-testing"></a>单元测试

可以使用标准测试框架像测试其他 Python 代码那样测试使用 Python 编写的函数。 对于大多数绑定，可以通过从 `azure.functions` 包创建适当类的实例来创建 mock 输入对象。 由于 [`azure.functions`](https://pypi.org/project/azure-functions/) 包不可供立即可用，请务必通过 `requirements.txt` 文件安装该包，如上文[包管理](#package-management)部分所述。

以 my_second_function 为例，下面是 HTTP 触发器函数的模拟测试：

首先需要创建 <project_root>/my_second_function/function.json 文件并将此函数定义为 http 触发器。

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

现在可以实现 my_second_function 和 shared_code.my_second_helper_function 。

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

我们可以开始为 http 触发器编写测试用例。

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

在 `.venv` Python 虚拟环境中安装你最喜欢的 Python 测试框架（例如 `pip install pytest`）。 然后运行 `pytest tests` 即可检查测试结果。

## <a name="temporary-files"></a>临时文件

`tempfile.gettempdir()` 方法返回一个临时文件夹，这在 Linux 上为 `/tmp`。 应用程序可使用此目录存储函数在执行期间生成和使用的临时文件。

> [!IMPORTANT]
> 不能保证写入临时目录的文件会在调用之间保留。 在横向扩展期间，临时文件不会在实例之间进行共享。

以下示例在临时目录 (`/tmp`) 中创建一个命名的临时文件：

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

建议你在独立于项目文件夹的文件夹中维护测试。 这样可防止对应用部署测试代码。

## <a name="preinstalled-libraries"></a>预安装的库

Python 函数运行时附带了一些库。

### <a name="python-standard-library"></a>Python 标准库

Python 标准库包含每个 Python 发行版附带的内置 Python 模块列表。 其中的大多数库有助于你访问系统功能，如文件 I/O。 在 Windows 系统上，这些库随 Python 一起安装。 在基于 Unix 的系统上，它们由包集合提供。

若要查看这些库的列表的完整详细信息，请访问以下链接：

* [Python 3.6 标准库](https://docs.python.org/3.6/library/)
* [Python 3.7 标准库](https://docs.python.org/3.7/library/)
* [Python 3.8 标准库](https://docs.python.org/3.8/library/)
* [Python 3.9 标准库](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions Python 辅助角色依赖项

Functions Python 辅助角色需要一组特定的库。 你也可以在函数中使用这些库，但它们并不属于 Python 标准库。 如果你的函数依赖于这些库中的任何一个库，则在 Azure Functions 之外运行时，这些函数可能无法用于代码。 可在 [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) 文件中的 install\_requires 部分找到依赖项的详细列表。

> [!NOTE]
> 如果函数应用的 requirements.txt 包含 `azure-functions-worker` 条目，请将其删除。 函数辅助角色由 Azure Functions 平台自动管理，我们会定期更新新功能和 Bug 修补程序。 在 requirements.txt 中手动安装旧版本的辅助角色可能会导致意外问题。

> [!NOTE]
>  如果你的包包含可能与辅助角色的依赖项冲突的某些库（例如 protobuf、tensorflow、grpcio），请在应用设置中将 [`PYTHON_ISOLATE_WORKER_DEPENDENCIES`](functions-app-settings.md#python_isolate_worker_dependencies-preview) 配置为 `1` 以防止应用程序引用辅助角色的依赖项。 此功能为预览版。

### <a name="azure-functions-python-library"></a>Azure Functions Python 库

每次 Python 辅助角色更新都包含一个新版本的 [Azure Functions Python 库 (azure.functions)](https://github.com/Azure/azure-functions-python-library)。 这种方法使持续更新 Python 函数应用变得更容易，因为每次更新都是向后兼容的。 可在 [azure-functions PyPi](https://pypi.org/project/azure-functions/#history) 中找到此库的版本列表。

运行时库版本由 Azure 修复，不能通过 requirements.txt 替代。 requirements.txt 中的 `azure-functions` 条目仅供 Lint 分析和客户认知。

使用以下代码在运行时中跟踪 Python 函数库的实际版本：

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>运行时系统库

有关 Python 辅助角色 Docker 映像中预安装的系统库列表，请访问以下链接：

|  Functions 运行时  | Debian 版本 | Python 版本 |
|------------|------------|------------|
| 版本 2.x | 拉伸  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| 3\.x 版 | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="python-worker-extensions"></a>Python 辅助角色扩展  

在 Azure Functions 中运行的 Python 工作进程允许将第三方库集成到函数应用中。 这些扩展库充当中间件，可以在执行函数的生命周期内注入特定操作。 

在与标准 Python 库模块类似的函数代码中导入扩展。 根据以下作用域执行扩展： 

| 范围 | 说明 |
| --- | --- |
| **应用程序级** | 导入到任何函数触发器中时，扩展适用于应用中的每个函数执行。 |
| **函数级** | 执行仅限于导入到其中的特定函数触发器。 |

查看给定扩展的信息，以了解有关扩展运行范围的详细信息。 

扩展实现了 Python 辅助角色扩展接口，该接口允许 Python 工作进程在函数执行生命周期内调入扩展代码。 若要了解详细信息，请参阅[创建扩展](#creating-extensions)。

### <a name="using-extensions"></a>使用扩展 

可以按照以下基本步骤在 Python 函数中使用 Python 辅助角色扩展库：

1. 在项目的 requirements.txt 文件中添加扩展包。
1. 将库安装到应用中。
1. 添加应用程序设置 `PYTHON_ENABLE_WORKER_EXTENSIONS`：
    + 本地：在 [local.settings.json 文件](functions-develop-local.md#local-settings-file)的 `Values` 部分中添加 `"PYTHON_ENABLE_WORKER_EXTENSIONS": "1"`
    + Azure：将 `PYTHON_ENABLE_WORKER_EXTENSIONS=1` 添加到[应用设置](functions-how-to-use-azure-function-app-settings.md#settings)。
1. 将扩展模块导入到函数触发器中。 
1. 配置扩展实例（如果需要）。 应在扩展文档中调出配置要求。 

> [!IMPORTANT]
> Microsoft 不支持或保证第三方 Python 辅助角色扩展库。 必须确保函数应用中使用的任何扩展都可信，并且你将承担使用恶意或编写不当的扩展的全部风险。 

第三方应提供有关如何在函数应用中安装并使用其特定扩展的特定文档。 有关如何使用扩展的基本示例，请参阅[使用扩展](develop-python-worker-extensions.md#consume-your-extension-locally)。 

下面是在函数应用中按范围使用扩展的示例：

# <a name="application-level"></a>[应用程序级](#tab/application-level)

```python
# <project_root>/requirements.txt
application-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from application_level_extension import AppExtension
AppExtension.configure(key=value)

def main(req, context):
  # Use context.app_ext_attributes here
```
# <a name="function-level"></a>[函数级](#tab/function-level)
```python
# <project_root>/requirements.txt
function-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from function_level_extension import FuncExtension
func_ext_instance = FuncExtension(__file__)

def main(req, context):
  # Use func_ext_instance.attributes here
```
---

### <a name="creating-extensions"></a>创建扩展 

扩展是由已创建可集成到 Azure Functions 中的功能的第三方库开发人员创建的。  扩展开发人员设计、实现和发布 Python 包，其中包含专为在函数执行上下文中运行而设计的自定义逻辑。 这些扩展可以发布到 PyPI 注册表或 GitHub 存储库。

若要了解如何创建、打包、发布和使用 Python 辅助角色扩展包，请参阅[为 Azure Functions 开发 Python 辅助角色扩展](develop-python-worker-extensions.md)。

#### <a name="application-level-extensions"></a>应用程序级扩展

继承自应用程序范围内的 [`AppExtensionBase`](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py) 运行的扩展。 

`AppExtensionBase` 公开以下抽象类方法以供你实现：

| 方法 | 说明 |
| --- | --- |
| **`init`** | 在导入扩展后调用。 |
| **`configure`** | 需要时从函数代码中调用以配置扩展。 |
| **`post_function_load_app_level`** | 在加载函数后立即调用。 函数名称和函数目录传递给扩展。 请记住，函数目录是只读的，在此目录中写入本地文件的任何尝试都会失败。 |
| **`pre_invocation_app_level`** | 在触发函数前立即调用。 函数上下文和函数调用参数传递给扩展。 通常可以传递上下文对象中的其他属性，以供函数代码使用。 |
| **`post_invocation_app_level`** | 在函数执行完成后立即调用。 函数上下文、函数调用参数和调用返回对象传递给扩展。 此实现是验证是否成功执行了生命周期挂钩的好方法。 |

#### <a name="function-level-extensions"></a>函数级扩展

继承自特定函数触发器中的 [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) 运行的扩展。 

`FuncExtensionBase` 公开以下抽象类方法以进行实现：

| 方法 | 说明 |
| --- | --- |
| **`__init__`** | 此方法是扩展的构造函数。 当在特定函数中初始化扩展实例时，将调用此方法。 实现此抽象方法时，可能需要接受 `filename` 参数，并将其传递给父级的方法 `super().__init__(filename)` 以进行适当的扩展注册。 |
| **`post_function_load`** | 在加载函数后立即调用。 函数名称和函数目录传递给扩展。 请记住，函数目录是只读的，在此目录中写入本地文件的任何尝试都会失败。 |
| **`pre_invocation`** | 在触发函数前立即调用。 函数上下文和函数调用参数传递给扩展。 通常可以传递上下文对象中的其他属性，以供函数代码使用。 |
| **`post_invocation`** | 在函数执行完成后立即调用。 函数上下文、函数调用参数和调用返回对象传递给扩展。 此实现是验证是否成功执行了生命周期挂钩的好方法。 |

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Python 函数应用完全支持 CORS。

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

下面列出了常见问题的疑难解答指南：

* [ModuleNotFoundError 和 ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [无法导入“cygrpc”](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

所有已知问题和功能请求都使用 [GitHub 问题](https://github.com/Azure/azure-functions-python-worker/issues)列表进行跟踪。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [Azure Functions 包 API 文档](/python/api/azure-functions/azure.functions)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Blob 存储绑定](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)
* [存储绑定](functions-bindings-storage-queue.md)
* [计时器触发器](functions-bindings-timer.md)

[存在问题？请告诉我们。](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse
