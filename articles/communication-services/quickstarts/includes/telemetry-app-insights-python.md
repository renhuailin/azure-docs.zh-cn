---
title: 包含文件
description: include 文件
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: 6671cd542ece13385c99a7c3b5787d37703894af
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292433"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7 或 3.6+。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。
- 在 Azure 门户中创建 [Application Insights 资源](../../../azure-monitor/app/create-new-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

   ```console
   mkdir application-insights-quickstart && cd application-insights-quickstart
   ```

1. 使用文本编辑器在项目根目录中创建一个名为“application-insights-quickstart.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

   ```python
    import os
    from opentelemetry import trace
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

    from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，安装适用于 Python 包的 Azure 通信服务标识 SDK 和适用于 Azure Monitor 的 Microsoft Opentelemetry Exporter。

```console
pip install azure-communication-identity
pip install azure-monitor-opentelemetry-exporter --pre
```

## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>通过调用通信标识 SDK 设置遥测跟踪器

使用连接字符串实例化 `CommunicationIdentityClient`。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

在 `try` 块内添加此代码：

```python
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

首先，若要创建用于在 Azure Monitor 中跟踪请求的 span，必须创建一个 `AzureMonitorTraceExporter` 对象实例。 你需要提供 Application Insights 资源的连接字符串。

```python
exporter = AzureMonitorTraceExporter.from_connection_string(
    "<APPLICATION-INSIGHTS-RESOURCE-CONNECTION-STRING>"
)
```

然后，此导出程序会允许你创建以下实例，以便进行请求跟踪。 创建 `AzureMonitorTraceExporter` 之后添加以下代码：

```python
    trace.set_tracer_provider(TracerProvider())
    tracer = trace.get_tracer(__name__)
    span_processor = BatchSpanProcessor(exporter)
    trace.get_tracer_provider().add_span_processor(span_processor)
```
初始化跟踪器后，便可以创建将负责跟踪请求的 span。

```python
with tracer.start_as_current_span(name="MyIdentityApplication"):
    user = identity_client.create_user()
```

## <a name="run-the-code"></a>运行代码

从控制台提示符导航到包含 application-insights-quickstart.py 文件的目录，然后执行以下 `python` 命令来运行应用。

```console
python application-insights-quickstart.py
```