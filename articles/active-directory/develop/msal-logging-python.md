---
title: 记录 MSAL for Python 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何记录 MSAL for Python 中的错误和异常
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578747"
---
# <a name="logging-in-msal-for-python"></a>MSAL for Python 中的日志记录

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>适用于 Python 的 MSAL 日志记录

MSAL for Python 中的日志记录利用 [python 标准库中的日志记录模块](https://docs.python.org/3/library/logging.html)。 可如下配置 MSAL 日志记录（并在 [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) 中查看实际效果）：

### <a name="enable-debug-logging-for-all-modules"></a>为所有模块启用调试日志记录

默认已禁用任何 Python 脚本中的日志记录。 如果要为脚本中的“所有” Python 模块启用详细日志记录，请使用级别为 `logging.DEBUG` 的 `logging.basicConfig`：

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

这会将提供给日志记录模块的所有日志消息都打印到标准输出。

### <a name="configure-msal-logging-level"></a>配置 MSAL 日志记录级别

可通过使用记录程序名为 `"msal"` 的 `logging.getLogger()` 方法来配置 MSAL for Python 日志提供程序的日志记录级别：

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>使用 Azure APP Insights 配置 MSAL 日志记录

将 Python 日志提供给日志处理程序，默认情况下为 `StreamHandler`。 若要使用检测密钥将 MSAL 日志发送到 Application Insights，请使用 `opencensus-ext-azure` 库提供的 `AzureLogHandler`。

若要安装，`opencensus-ext-azure` 将 PyPI 中的 `opencensus-ext-azure` 包添加到依赖项或 pip 安装：

```console
pip install opencensus-ext-azure
```

然后使用 `APP_INSIGHTS_KEY` 环境变量中设置的检测密钥将 `"msal"` 日志提供程序的默认处理程序更改为 `AzureLogHandler` 的实例：

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Python 中的个人和组织数据

适用于 Python 的 MSAL 不会记录个人数据或组织数据。 没有任何属性可用于启用或者禁用个人或组织数据的日志记录。

你可以使用标准的 Python 日志记录来记录所需的任何内容，但需要责任安全处理敏感数据并遵守法规要求。

有关 Python 中的日志记录的详细信息，请参阅 Python 的[日志记录：操作指南](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)。

## <a name="next-steps"></a>后续步骤

有关更多代码示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。
