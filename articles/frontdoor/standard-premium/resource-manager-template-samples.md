---
title: 资源管理器模板示例 - Azure Front Door
description: 有关为 Azure Front Door 提供的示例 Azure 资源管理器模板的信息。
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561740"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>用于 Azure Front Door 的 Azure 资源管理器模板

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

下表包含指向适用于 Azure Front Door 的 Azure 资源管理器模板的链接，以及包括其他 Azure 服务在内的参考体系结构。

| 示例 | 说明 |
|-|-|
| [规则集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | 创建 Front Door 配置文件和规则集。  |
|**应用服务源**| **说明** |
| [应用服务](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | 创建具有公共终结点的应用服务应用以及 Front Door 配置文件。  |
| [具有专用链接的应用服务](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | 创建具有专用终结点的应用服务应用以及 Front Door 配置文件。  |
| [具有专用链接的应用服务环境](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | 创建应用服务环境、具有专用终结点的应用以及 Front Door 配置文件。  |
|**Azure Functions 源**| **说明** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | 创建具有公共终结点的 Azure Functions 应用以及 Front Door 配置文件。  |
| [具有专用链接的 Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | 创建具有专用终结点的 Azure Functions 应用以及 Front Door 配置文件。  |
|**API 管理源**| **说明** |
| [API 管理（外部）](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | 创建具有外部 VNet 集成的 API 管理实例以及 Front Door 配置文件。  |
|**存储源**| **说明** |
| [存储静态网站](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | 创建具有公共终结点的 Azure 存储帐户和静态网站以及 Front Door 配置文件。  |
| [具有专用链接的存储 blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | 创建具有专用终结点的 Azure 存储帐户和 blob 容器以及 Front Door 配置文件。  |
|**应用程序网关源**| **说明** |
| [应用程序网关](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | 创建应用程序网关和 Front Door 配置文件。 |
|**虚拟机源**| **说明** |
| [具有专用链接服务的虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | 创建虚拟机和专用链接服务以及 Front Door 配置文件。 |
| | |
