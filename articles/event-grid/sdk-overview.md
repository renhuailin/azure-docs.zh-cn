---
title: Azure 事件网格 SDK
description: 介绍 Azure 事件网格的 SDK。 这些 SDK 提供管理、发布和使用功能。
ms.topic: reference
ms.date: 05/17/2021
ms.openlocfilehash: 5e196f2dc57d7f6e4d7ef1040f6a65d2101cf0dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644973"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>用于管理和发布的事件网格 SDK

使用事件网格提供的 SDK 可以编程方式管理资源和发布事件。

## <a name="management-sdks"></a>管理 SDK

使用管理 SDK 可以创建、更新和删除事件网格主题和订阅。 目前有以下 SDK 可用：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-resourcemanager-eventgrid)
* [Node](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>数据平面 SDK

使用数据平面 SDK，可以通过处理身份验证、形成事件并以异步方式发布到指定终结点来将事件发布到主题。 还可以通过这些 SDK 使用第一方事件。 目前有以下 SDK 可用：

| 编程语言 | SDK 中 IsInRole 中的声明 | 
| -------------------- | ---------- | 
| .NET | 最新稳定版 SDK：[Azure.Messaging.EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/)<p>旧版 SDK：[Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) |
| Java | 最新稳定版 SDK：[azure-messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)<p>旧版 SDK：[azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)</p> |  
| Python | [azure-eventgrid](https://pypi.org/project/azure-eventgrid/) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) | 
| Go | [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>后续步骤

* 有关示例应用程序，请参阅[事件网格代码示例](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)。
* 有关事件网格的介绍，请参阅[什么是事件网格？](overview.md)
* 有关 Azure CLI 中的事件网格命令，请参阅 [Azure CLI](/cli/azure/eventgrid)。
* 有关 PowerShell 中的事件网格命令，请参阅 [PowerShell](/powershell/module/az.eventgrid)。
