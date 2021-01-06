---
title: 在 Azure Functions 中提高 Python 应用的吞吐量性能
description: 了解如何使用在负载下非常高性能和缩放良好的 Python 开发 Azure Functions 应用。
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: baa362f029678f266f154df912a9178a6626667d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935863"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>在 Azure Functions 中提高 Python 应用的吞吐量性能

使用 Python 为 Azure Functions 进行开发时，需要了解函数的执行方式以及该性能如何影响函数应用的缩放方式。 设计高性能应用程序时，此需求更重要。 设计、编写和配置函数应用时要考虑的主要因素是水平缩放和吞吐量性能配置。

## <a name="horizontal-scaling"></a>水平扩展
默认情况下，Azure Functions 会自动监视应用程序的负载，并根据需要为 Python 创建其他主机实例。 Azure Functions 使用不同触发器类型的内置阈值来决定何时添加实例，如 QueueTrigger 的消息和队列大小的期限。 这些阈值不是用户可配置的。 有关详细信息，请参阅 [Azure Functions 中事件驱动的缩放](event-driven-scaling.md)。

## <a name="improving-throughput-performance"></a>提高吞吐量性能

默认配置适用于大多数 Azure Functions 应用程序。 不过，你可以通过基于工作负荷配置文件使用配置来提高应用程序吞吐量的性能。 第一步是了解正在运行的工作负荷的类型。

|| I/o 绑定的工作负荷 | CPU 绑定工作负荷 |
|--| -- | -- |
|函数应用特征| <ul><li>应用需要处理多个并发调用。</li> <li> 应用处理大量 i/o 事件，例如网络调用和磁盘读/写。</li> </ul>| <ul><li>应用执行长时间运行的计算，例如调整图像大小。</li> <li>应用进行数据转换。</li> </ul> |
|示例| <ul><li>Web API</li><ul> | <ul><li>数据处理</li><li> 机器学习推理</li><ul>|

 
由于现实世界的工作负荷通常是 i/o 和 CPU 的混合，因此应在实际的生产负载下分析应用。


### <a name="performance-specific-configurations"></a>特定于性能的配置

了解 function app 的工作负荷配置文件后，可以使用以下配置来提高函数的吞吐量性能。

* [异步](#async)
* [多语言辅助角色](#use-multiple-language-worker-processes)
* [语言工作进程中的最大工作线程数](#set-up-max-workers-within-a-language-worker-process)
* [事件循环](#managing-event-loop)
* [垂直缩放](#vertical-scaling)



#### <a name="async"></a>Async

由于 [python 是单线程运行时](https://wiki.python.org/moin/GlobalInterpreterLock)，因此默认情况下，用于 python 的主机实例一次只能处理一个函数调用。 对于处理大量 i/o 事件和/或 i/o 绑定的应用程序，你可以通过异步运行函数来显著提高性能。

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


不带关键字的函数 `async` 在 ThreadPoolExecutor 线程池中自动运行：

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

为了获得异步运行函数的全部好处，你的代码中使用的 i/o 操作/库还需要实现异步。 在定义为异步的函数中使用同步 i/o 操作 **可能会** 影响整体性能。 如果你使用的库未实现异步版本，则在应用中 [管理事件循环](#managing-event-loop) 后，你仍可以通过异步运行代码来受益。 

下面是已实现异步模式的几个客户端库示例：
- [aiohttp](https://pypi.org/project/aiohttp/) -asyncio 的 Http 客户端/服务器 
- [流 API](https://docs.python.org/3/library/asyncio-stream.html) -用于处理网络连接的高级异步/等待就绪基元
- [Janus 队列](https://pypi.org/project/janus/) -线程安全 asyncio 感知队列
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ 的 Python 绑定
 
##### <a name="understanding-async-in-python-worker"></a>了解 python 辅助角色中的异步

`async`在函数签名前面定义时，Python 会将函数标记为协同程序。 调用协同程序时，可以将其作为任务计划到事件循环中。 `await`在异步函数中调用时，它会将延续注册到事件循环中，并允许事件循环在等待时间内处理下一任务。

在 Python 辅助角色中，辅助角色与客户的职能共享事件循环， `async` 并且可以同时处理多个请求。 我们强烈建议客户使用 asyncio 兼容库 (例如 [aiohttp](https://pypi.org/project/aiohttp/)、 [pyzmq](https://pypi.org/project/pyzmq/)) 。 与以同步方式实现的这些库相比，采用这些建议会大幅提高函数的吞吐量。

> [!NOTE]
>  如果在其实现中将函数声明为 `async` `await` ，则函数的性能将受到严重影响，因为阻止 python 工作线程处理并发请求的事件循环。

#### <a name="use-multiple-language-worker-processes"></a>使用多个语言工作进程

默认情况下，每个函数主机实例都有一个语言工作进程。 使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置可增加每个主机的工作进程数（最多 10 个）。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。

对于 CPU 绑定的应用，应将语言辅助角色的数量设置为等于或高于每个 function app 可用的内核数。 若要了解详细信息，请参阅 [可用实例 sku](functions-premium-plan.md#available-instance-skus)。 

I/o 绑定的应用程序的数量也可能会增加，因为这会增加超出可用核心数的工作进程数。 请记住，由于所需的上下文切换次数增加，将工作线程数设置得过高可能会影响整体性能。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>在语言工作进程中设置最大工作线程数

如 async [部分](#understanding-async-in-python-worker)所述，Python 语言辅助角色处理函数和 [协同程序](https://docs.python.org/3/library/asyncio-task.html#coroutines) 的方式不同。 协同程序在运行语言辅助角色的同一事件循环中运行。 另一方面，函数调用在由语言辅助角色维护的 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)中运行，作为一个线程。

您可以使用 " [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) 应用程序" 设置，设置运行同步功能的最大工作线程数的值。 此值设置 `max_worker` ThreadPoolExecutor 对象的参数，该参数允许 Python 使用最多线程的池 `max_worker` 异步执行调用。 `PYTHON_THREADPOOL_THREAD_COUNT`适用于函数主机创建的每个工作线程，Python 决定何时创建新线程或重复使用现有的空闲线程。 对于较旧的 Python 版本 (为、 `3.8` 、 `3.7` 和 `3.6`) ， `max_worker` 将值设置为1。 对于 Python 版本 `3.9` ，将 `max_worker` 设置为 `None` 。

对于与 CPU 绑定的应用程序，应将此设置保留为一个较小的数字，从1开始，并在您试验工作负荷时增加。 这一建议可缩短在上下文切换上花费的时间，并使 CPU 绑定任务完成。

对于 i/o 绑定的应用程序，你应该通过增加处理每个调用的线程的数量来发现巨大的收益。 建议从 Python 默认值开始（核心数 + 4），然后根据你所看到的吞吐量值进行调整。

对于混合工作负荷应用，应平衡 `FUNCTIONS_WORKER_PROCESS_COUNT` 和 `PYTHON_THREADPOOL_THREAD_COUNT` 配置以最大程度地提高吞吐量。 若要了解函数应用最多花费的时间，建议对其进行分析，并根据其出现的行为设置值。 另请参阅此 [部分](#use-multiple-language-worker-processes) ，了解 FUNCTIONS_WORKER_PROCESS_COUNT 应用程序设置。

> [!NOTE]
>  尽管这些建议适用于 HTTP 和非 HTTP 触发的函数，但你可能需要调整非 HTTP 触发函数的其他触发器特定配置，以获得函数应用的预期性能。 有关此内容的详细信息，请参阅此 [文](functions-best-practices.md)。


#### <a name="managing-event-loop"></a>管理事件循环

应使用 asyncio 兼容的第三方库。 如果任何第三方库都无法满足你的需求，则还可以在 Azure Functions 中管理事件循环。 管理事件循环使你能够更灵活地进行计算资源管理，并且还可以将同步 i/o 库打包到协同程序中。

使用内置的 **asyncio** 库，可以使用许多有用的 Python 官方文档讨论 [协同程序和任务](https://docs.python.org/3/library/asyncio-task.html)和 [事件循环](https://docs.python.org/3.8/library/asyncio-eventloop.html)。

以下面的 [请求](https://github.com/psf/requests) 库为例，此代码片段使用 **asyncio** 库将 `requests.get()` 方法包装到协同程序中，同时运行多个 web 请求以同时 SAMPLE_URL。


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
对于更多的处理单元，尤其是在与 CPU 绑定的操作中，您可以通过升级到具有更高规范的高级计划来实现此操作。 使用较高的处理单元，你可以根据可用的内核数调整工作进程计数的数量，并实现更高的并行度。 

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Python 开发的详细信息，请参阅以下资源：

* [Azure Functions Python 开发人员指南](functions-reference-python.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)

