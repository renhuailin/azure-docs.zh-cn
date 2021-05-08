---
title: 在 Azure Functions 中提高 Python 应用的吞吐量性能
description: 了解如何使用在负载下非常高性能和扩展良好的 Python 开发 Azure Functions 应用。
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786100"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>在 Azure Functions 中提高 Python 应用的吞吐量性能

使用 Python 为 Azure Functions 进行开发时，需要了解函数的执行方式以及该性能如何影响函数应用的扩展方式。 设计高性能应用时，此需求更重要。 设计、编写和配置函数应用时要考虑的主要因素是水平扩展和吞吐量性能配置。

## <a name="horizontal-scaling"></a>水平扩展
默认情况下，Azure Functions 会自动监视应用程序的负载，并根据需要为 Python 创建其他主机实例。 Azure Functions 针对不同触发器类型使用内置阈值来确定何时添加实例，例如 QueueTrigger 的消息年龄和队列大小。 这些阈值不是用户可配置的。 有关详细信息，请参阅 [Azure Functions 中事件驱动的扩展](event-driven-scaling.md)。

## <a name="improving-throughput-performance"></a>提高吞吐量性能

默认配置适用于大多数 Azure Functions 应用程序。 不过，可以通过使用基于工作负荷配置文件的配置来提高应用程序吞吐量的性能。 第一步是了解正在运行的工作负荷的类型。

| 工作负荷类型 | 函数应用特征       | 示例                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| “I/O 绑定”     | • 应用需要处理许多并发调用。<br>• 应用处理大量 I/O 事件，例如网络调用和磁盘读/写。 | • Web API                                          |
| “CPU 绑定”     | • 应用执行长时间运行的计算，例如重设图像大小。<br>• 应用执行数据转换。                                                | • 数据处理<br>• 机器学习推理<br> |

 
由于现实世界的函数工作负荷通常是 I/O 和 CPU 绑定的混合，因此应在实际的生产负荷下分析应用。


### <a name="performance-specific-configurations"></a>特定于性能的配置

了解函数应用的工作负荷配置文件后，可以使用以下配置来提高函数的吞吐量性能。

* [异步](#async)
* [多语言工作进程](#use-multiple-language-worker-processes)
* [语言工作进程中的最大工作进程数](#set-up-max-workers-within-a-language-worker-process)
* [事件循环](#managing-event-loop)
* [垂直扩展](#vertical-scaling)



#### <a name="async"></a>异步

由于 [Python 是单线程运行时](https://wiki.python.org/moin/GlobalInterpreterLock)，因此 Python 的主机实例在默认情况下一次只能处理一次函数调用。 对于处理大量 I/O 事件和/或受 I/O 绑定的应用程序，可以通过以异步方式运行函数来提高性能。

若要以异步方式运行函数，请使用 `async def` 语句，该语句直接使用 [asyncio](https://docs.python.org/3/library/asyncio.html) 运行该函数：

```python
async def main():
    await some_nonblocking_socket_io_op()
```
下面是一个使用 HTTP 触发器的函数的示例，该函数使用 [aiohttp](https://pypi.org/project/aiohttp/) http 客户端：

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


没有 `async` 关键字的函数在 ThreadPoolExecutor 线程池中自动运行：

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

为了获得以异步方式运行函数的全部好处，代码中使用的 I/O 操作/库也需要实现异步。 在定义为异步的函数中使用同步 I/O 操作“可能会影响”整体性能。  如果使用的库未实现异步版本，则在应用中[管理事件循环](#managing-event-loop)后，仍可以通过异步运行代码来受益。 

下面是已实现异步模式的几个客户端库示例：
- [aiohttp](https://pypi.org/project/aiohttp/) - asyncio 的 Http 客户端/服务器 
- [流 API](https://docs.python.org/3/library/asyncio-stream.html) - 用于处理网络连接的高级异步/等待就绪基元
- [Janus 队列](https://pypi.org/project/janus/) - Python 的线程安全 asyncio 感知队列
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ 的 Python 绑定
 
##### <a name="understanding-async-in-python-worker"></a>了解 python 工作进程中的异步

在函数签名前面定义 `async` 时，Python 会将函数标记为协同例程。 调用协同例程时，可以将其作为任务计划到事件循环中。 在异步函数中调用 `await` 时，它会将延续注册到事件循环中，并允许事件循环在等待时间内处理下一任务。

在 Python 工作进程中，工作进程与客户的 `async` 函数共享事件循环，并且可以同时处理多个请求。 我们强烈建议客户使用 asyncio 兼容库（例如 [aiohttp](https://pypi.org/project/aiohttp/)、[pyzmq](https://pypi.org/project/pyzmq/)）。 与以同步方式实现的这些库相比，采用这些建议会大幅提高函数的吞吐量。

> [!NOTE]
>  如果将函数声明为 `async` 而在其实现中没有任何 `await`，则函数的性能将受到严重影响，因为将阻止事件循环，从而禁止 python 工作进程处理并发请求。

#### <a name="use-multiple-language-worker-processes"></a>使用多个语言工作进程

默认情况下，每个函数主机实例都有一个语言工作进程。 使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置可增加每个主机的工作进程数（最多 10 个）。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。

对于 CPU 绑定的应用，应将语言工作进程的数量设置为等于或高于每个函数应用可用的内核数。 若要了解详细信息，请参阅[可用实例 SKU](functions-premium-plan.md#available-instance-skus)。 

I/O 绑定的应用的数量也可能会得益于工作进程数增加到超过可用的内核数。 请记住，由于所需的上下文切换次数增加，将工作进程数设置得过高可能会影响整体性能。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>设置语言工作进程中的最大工作进程数

如 async [部分](#understanding-async-in-python-worker)所述，Python 语言工作进程处理函数和[协同例程](https://docs.python.org/3/library/asyncio-task.html#coroutines)的方式不同。 协同例程在运行语言工作进程的同一事件循环中运行。 另一方面，函数调用在由语言工作进程作为一个线程维护的 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor) 中运行。

可以使用 [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) 应用程序设置来设置运行同步函数的最大工作进程数的值。 此值设置 ThreadPoolExecutor 对象的 `max_worker` 自变量，该自变量允许 Python 使用最多 `max_worker` 个线程的池来以异步方式执行调用。 `PYTHON_THREADPOOL_THREAD_COUNT` 适用于函数主机创建的每个工作进程，Python 决定何时创建新线程或重复使用现有的空闲线程。 对于较旧的 Python 版本（即，`3.8`、`3.7` 和 `3.6`），`max_worker` 值设置为 1。 对于 Python 版本 `3.9`，`max_worker` 设置为 `None`。

对于 CPU 绑定的应用，应将此设置保持为一个较小的数字，从 1 开始，并随着试验工作负荷而增大。 这一建议旨在缩短在上下文切换上花费的时间，并使 CPU 绑定任务完成。

对于 I/O 绑定的应用，应该通过增大处理每个调用的线程的数量来发现巨大的收益。 建议从 Python 默认值开始（内核数 + 4），然后根据你所看到的吞吐量值进行调整。

对于混合工作负荷应用，应平衡 `FUNCTIONS_WORKER_PROCESS_COUNT` 和 `PYTHON_THREADPOOL_THREAD_COUNT` 配置以最大程度地提高吞吐量。 若要了解函数应用把最多的时间花费在了哪里，建议对其进行分析，并根据其出现的行为设置值。 另请参阅此[部分](#use-multiple-language-worker-processes)，了解 FUNCTIONS_WORKER_PROCESS_COUNT 应用程序设置。

> [!NOTE]
>  尽管这些建议适用于 HTTP 和非 HTTP 触发的函数，但你可能需要调整非 HTTP 触发函数的其他触发器特定的配置，以获得函数应用的预期性能。 有关详细信息，请参阅[此文](functions-best-practices.md)。


#### <a name="managing-event-loop"></a>管理事件循环

应使用 asyncio 兼容的第三方库。 如果任何第三方库都无法满足你的需求，则还可以在 Azure Functions 中管理事件循环。 管理事件循环使你能够更灵活地进行计算资源管理，并且还可以将同步 I/O 库打包到协同例程中。

有许多有用的 Python 官方文档讨论使用内置“asyncio”库的[协同例程和任务](https://docs.python.org/3/library/asyncio-task.html)和[事件循环](https://docs.python.org/3.8/library/asyncio-eventloop.html)。

以下面的[请求](https://github.com/psf/requests)库为例，此代码片段使用“asyncio”库将 `requests.get()` 方法打包到协同例程中，从而同时运行多个去往SAMPLE_UR 的 web 请求。


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>垂直缩放
对于更多的处理单元，尤其是在 CPU 绑定的操作中，可以通过升级到具有更高规格的高级计划来实现此操作。 使用较高的处理单元，可以根据可用的内核数调整工作进程计数的数量，并实现更高的并行度。 

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Python 开发的详细信息，请参阅以下资源：

* [Azure Functions Python 开发人员指南](functions-reference-python.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)

