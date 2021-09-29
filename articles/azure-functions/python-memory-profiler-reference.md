---
title: Azure Functions Python 应用内存分析
description: 了解如何分析 Python 应用的内存用量并确定内存瓶颈。
ms.topic: how-to
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f894f369fa1efdb80160761e3c58cad937708b08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737429"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>分析 Azure Functions Python 应用的内存用量

在开发过程中或在将本地 Python 函数应用项目部署到 Azure 之后，最好分析函数中的潜在内存瓶颈。 此类瓶颈会削弱函数性能并导致错误。 以下说明演示了如何使用[内存探查器](https://pypi.org/project/memory-profiler) Python 包，其在执行时逐行分析函数的内存消耗。

> [!NOTE]
> 内存分析仅适用于开发环境的内存占用情况分析。 请不要在生产函数应用中使用内存探查器。

## <a name="prerequisites"></a>先决条件

开发 Python 函数应用必须满足以下要求：

* [Python 3.6.x or above](https://www.python.org/downloads/release/python-374/)。 若要查看 Azure Functions 中受支持 Python 版本的完整列表，请访问[Python 开发人员指南](functions-reference-python.md#python-version)。

* [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。

* 已在某个[支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安装 [Visual Studio Code](https://code.visualstudio.com/)。

* 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>内存分析过程

1. 在 requirements.txt 中添加`memory-profiler`，确保将包与部署捆绑在一起。 如果在本地计算机上开发，可能需要 [激活 Python 虚拟环境](create-first-function-cli-python.md#create-venv)，并使用`pip install -r requirements.txt`进行包解析。

2. 在函数脚本（通常为\_\_init\_\_.py），在`main()`中的函数上方添加下列行。 将确保根记录器报告子记录器名称，以便通过前缀`memory_profiler_logs`区分内存分析日志。

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>示例

以下示例解释了如何对异步和同步 HTTP 触发器（分别名为 "HttpTriggerAsync" 和 "HttpTriggerSync"）执行内存分析。 我们将创建仅向 Microsoft 主页发送 GET 请求的 Python 函数应用。

### <a name="create-a-python-function-app"></a>创建 Python 函数应用

Python 函数应用应沿用 Azure Functions 指定的 [文件夹结构](functions-reference-python.md#folder-structure)。 若要创建项目基架，建议通过运行以下命令使用 Azure Functions Core Tools：

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>更新文件内容

requirements.txt 定义项目使用的包。 除了 Azure Functions SDK 和内存探查器外，我们还介绍了异步 HTTP 请求的`aiohttp`与同步 HTTP 调用的`requests`。

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

还需重写异步 HTTP 触发器`HttpTriggerAsync/__init__.py`并配置内存探查器、根记录器格式和记录器流式处理绑定。

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

有关同步 HTTP 触发器，请参阅下列`HttpTriggerSync/__init__.py`代码部分：

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>分析本地开发环境中的 Python 函数应用

完成上述更改后还需几个步骤即可为 Azure Functions 运行时初始化 Python 虚拟环境。

1. 根据喜好打开 Windows PowerShell 或任何 Linux shell。
2. 通过 Windows 的`py -m venv .venv`或 Linux 的`python3 -m venv .venv`创建 Python 虚拟环境。
3. 通过 Windows PowerShell 的`.venv\Scripts\Activate.ps1`或 Linux shell 的`source .venv/bin/activate`激活 Python 虚拟环境。
4. 通过`pip install requirements.txt`还原 Python 依赖项
5. 通过 Azure Functions Core Tools`func host start`启动本地 Azure Functions runtime 运行时
6. 将 GET 请求发送至`https://localhost:7071/api/HttpTriggerAsync` 或 `https://localhost:7071/api/HttpTriggerSync`。
7. Azure Functions Core Tools 应显示与以下部分相似的内存分析报告。

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Python 开发的详细信息，请参阅以下资源：

* [Azure Functions Python 开发人员指南](functions-reference-python.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
