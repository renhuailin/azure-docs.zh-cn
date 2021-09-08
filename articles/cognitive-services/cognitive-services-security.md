---
title: Azure 认知服务安全性
titleSuffix: Azure Cognitive Services
description: 了解有关使用认知服务的各种安全注意事项。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: b77eadcbd46c4cac442ed26af94d5781d225806a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475775"
---
# <a name="azure-cognitive-services-security"></a>Azure 认知服务安全性

开发任何应用程序时，都应将安全性视为重中之重。 随着支持人工智能的应用程序的出现，安全性变得更加重要。 本文概述了 Azure 认知服务安全性的各个方面，例如使用传输层安全性、身份验证、安全配置敏感数据以及用于客户数据访问的客户密码箱。

## <a name="transport-layer-security-tls"></a>传输层安全 (TLS) (Transport Layer Security) (TLS)

通过 HTTP 公开的所有认知服务终结点都强制执行 TLS 1.2。 使用强制执行的安全协议时，尝试调用认知服务终结点的使用者应遵循以下准则：

* 客户端操作系统 (OS) 需要支持 TLS 1.2
* 用于进行 HTTP 调用的语言（和平台）需要在请求中指定 TLS 1.2
  * 根据语言和平台，TLS 的指定可以通过隐式或显式方式完成

对于 .NET 用户，请考虑<a href="/dotnet/framework/network-programming/tls" target="_blank">传输层安全性最佳做法</a>。

## <a name="authentication"></a>身份验证

在讨论身份验证时，存在几种常见的误解。 身份验证和授权常常互相混淆。 标识也是安全性的主要组件。 标识是有关<a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">主体 </a> 的信息的集合。 标识提供者 (IdP) 为身份验证服务提供标识。 身份验证是验证用户身份的行为。 授权指为给定身份指定对资源的访问权限和特权。 有多种认知服务产品/服务，包括 Azure 基于角色的访问控制 (Azure RBAC)。 Azure RBAC 可用于简化与人工管理主体有关的一些仪式。 有关更多详细信息，请参阅 [Azure 资源的 Azure 基于角色的访问控制](../role-based-access-control/overview.md)。

有关使用订阅密钥、访问令牌和 Azure Active Directory (AAD) 进行身份验证的详细信息，请参阅<a href="/azure/cognitive-services/authentication" target="_blank">对 Azure 认知服务请求进行身份验证</a>。

## <a name="environment-variables-and-application-configuration"></a>环境变量和应用程序配置

环境变量是存储在特定环境中的名称/值对。 为敏感数据使用硬编码值的一种更安全的替代选项是使用环境变量。 硬编码值不安全，应避免使用。

> [!CAUTION]
> **不要** 为敏感数据使用硬编码值，此行为会导致重大安全漏洞。

> [!NOTE]
> 尽管环境变量以纯文本格式存储，但它们与环境隔离。 如果环境受到破坏，环境中的变量也会受到破坏。

### <a name="set-environment-variable"></a>设置环境变量

若要设置环境变量，请使用以下命令之一（其中 `ENVIRONMENT_VARIABLE_KEY` 是命名键，`value` 是存储在环境变量中的值）。

# <a name="command-line"></a>[命令行](#tab/command-line)

在给定值的情况下，创建并分配持久化环境变量。

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

在命令提示符的新实例中，读取环境变量。

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在给定值的情况下，创建并分配持久化环境变量。

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

在 Windows PowerShell 的新实例中，读取环境变量。

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

在给定值的情况下，创建并分配持久化环境变量。

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

在 Bash 的新实例中，读取环境变量。

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 设置环境变量后，请重启集成开发环境 (IDE)，以确保新添加的环境变量可用。

### <a name="get-environment-variable"></a>获取环境变量

若要获取环境变量，必须将其读入内存。 根据使用的语言，考虑使用以下代码片段。 这些代码片段演示了如何在给定 `ENVIRONMENT_VARIABLE_KEY` 的情况下获取环境变量并将其分配给名为 `value` 的变量。

# <a name="c"></a>[C#](#tab/csharp)

有关详细信息，请参阅 <a href="/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` </a>。

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

有关详细信息，请参阅 <a href="/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` </a>。

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

有关详细信息，请参阅 <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` </a>。

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

有关详细信息，请参阅 <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` </a>。

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

有关详细信息，请参阅 <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` </a>。

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

有关详细信息，请参阅 <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` </a>。

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>客户密码箱

[Microsoft Azure 客户密码箱](../security/fundamentals/customer-lockbox-overview.md)提供了一个界面，供客户查看和批准/拒绝客户数据访问请求。 当 Microsoft 工程师需要在支持请求期间访问客户数据时，可以使用此功能。 若要了解如何启动、跟踪客户密码箱请求以及如何对其进行存储以供稍后查看和审核，请参阅[客户密码箱](../security/fundamentals/customer-lockbox-overview.md)。 

客户密码箱可用于此服务：

* 翻译工具

对于以下服务，Microsoft 工程师将不会访问 E0 层级中的任何客户数据： 

* 语言理解
* 人脸
* 内容审查器
* 个性化体验创建服务

若要请求使用 E0 SKU，请填写并提交此 [请求表单](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 在被批准将 E0 SKU 与 LUIS 配合使用后，需要从 Azure 门户创建新资源并选择 E0 作为定价层。 用户将无法从 F0 升级到新的 E0 SKU。

语音服务目前不支持客户密码箱。 但是，可以使用“自带存储”(BYOS) 来存储客户数据，这样能够实现与客户密码箱类似的数据控制。 请记住，语音服务数据仍会保留，并会在创建语音资源的区域中进行处理。 这适用于任何静态数据和传输中的数据。 在使用自定义功能（如自定义语音识别和自定义语音）时，所有的客户数据都会在你的 BYOS（如果使用了）和语音服务资源所在的同一区域中传输、存储和处理。

> [!IMPORTANT]
> Microsoft 不会使用客户数据来改进其语音模型。 此外，如果禁用了终结点日志记录，并且未使用任何自定义，则不会存储任何客户数据。 

## <a name="next-steps"></a>后续步骤

* 探索各种[认知服务](./what-are-cognitive-services.md)
* 详细了解[认知服务虚拟网络](cognitive-services-virtual-networks.md)