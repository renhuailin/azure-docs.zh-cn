---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553427"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100))类分析配置设置。 它分析桌面、移动设备、Azure 虚拟机或 Azure 云服务中运行的客户端应用程序的设置。

若要引用 `CloudConfigurationManager` 包，请添加以下 `using` 指令：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

下面的示例演示了如何检索配置文件中的连接字符串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

可以选择使用 Azure Configuration Manager。 还可以使用 API，例如 .NET Framework 的 [ConfigurationManager 类](/dotnet/api/system.configuration.configurationmanager)。