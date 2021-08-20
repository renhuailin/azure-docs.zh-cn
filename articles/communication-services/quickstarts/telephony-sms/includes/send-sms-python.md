---
title: 包含文件
description: include 文件
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 9e44cc1f631a56d1948c744a1ecb912208bad11e
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113659573"
---
通过使用通信服务 Python 短信 SDK 发送短信，开始使用 Azure 通信服务。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/send-sms-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7 或 3.6+。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `python --version` 命令来查看是否安装了 Python。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir sms-quickstart && cd sms-quickstart
```

使用文本编辑器在项目根目录中创建名为“send-sms.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>安装包

如果仍在应用程序目录中，可以使用 `pip install` 命令安装适用于 Python 包的 Azure 通信服务短信 SDK。

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 Python 的 Azure 通信服务短信 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | 所有短信功能都需要此类。 使用订阅信息对其进行实例化，然后使用它发送短信。                                                                                                                 |
| SmsSendResult               | 此类包含来自短信服务的结果。                                          |

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 SmsClient。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
为简单起见，我们在本快速入门中使用连接字符串，但在生产环境中，我们建议使用[服务主体](../../../quickstarts/identity/service-principal.md)。

## <a name="send-a-11-sms-message"></a>发送 1:1 短信

若要将短信发送给单个收件人，请使用单个收件人电话号码从 SmsClient 调用 ```send``` 方法。 你还可以传入可选参数，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。 将此代码添加到 send-sms.py 中的 `try` 块的末尾：

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

应将 `<from-phone-number>` 替换为与通信服务关联的启用短信的电话号码，将 `<to-phone-number>` 替换为要向其发送消息的电话号码。

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

## <a name="send-a-1n-sms-message"></a>发送 1:N 短信

若要将短信发送给收件人列表，请使用收件人电话号码列表从 SmsClient 调用 ```send``` 方法。 你还可以传入可选参数，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。 将此代码添加到 send-sms.py 中的 `try` 块的末尾：

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

你应将 `<from-phone-number>` 替换为与通信服务关联的启用短信的电话号码，并将 `<to-phone-number-1>` 和 `<to-phone-number-2>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

## <a name="optional-parameters"></a>可选参数

`enable_delivery_report` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

`tag` 参数为可选参数，可用于将标记应用到传送报告。

## <a name="run-the-code"></a>运行代码
从应用程序目录使用 `python` 命令运行应用程序。

```console
python send-sms.py
```

完整的 Python 脚本应如下所示：

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
