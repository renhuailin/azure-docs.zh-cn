---
title: 资源管理器模板示例 - Azure Front Door
description: 有关为 Azure Front Door 提供的示例 Azure 资源管理器模板的信息。
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 05/11/2021
ms.openlocfilehash: e55628988d80f13b86481477d6f8e59ac4ceb30b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438577"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>用于 Azure Front Door 的 Azure 资源管理器模板

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

下表包含指向适用于 Azure Front Door 的 Azure 资源管理器模板的链接，以及包括其他 Azure 服务在内的参考体系结构。

| 示例 | 说明 |
|-|-|
| [Front Door（快速创建）](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | 创建 Front Door 基本配置文件，包括终结点、源组、源和路由。  |
| [规则集](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | 创建 Front Door 配置文件和规则集。  |
| [具有托管规则集的 WAF 策略](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | 创建 Front Door 配置文件和具有托管规则集的 WAF。  |
| [具有自定义规则的 WAF 策略](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | 创建 Front Door 配置文件和具有自定义规则的 WAF。  |
| [具有速率限制的 WAF 策略](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | 创建 Front Door 配置文件和具有用于执行速率限制的自定义规则的 WAF。  |
| [带地区筛选的 WAF 策略](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | 创建 Front Door 配置文件和具有用于执行地区筛选的自定义规则的 WAF。  |
|**应用服务源**| **说明** |
| [应用服务](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | 创建具有公共终结点的应用服务应用以及 Front Door 配置文件。  |
| [具有专用链接的应用服务](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | 创建具有专用终结点的应用服务应用以及 Front Door 配置文件。  |
|**Azure Functions 源**| **说明** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | 创建具有公共终结点的 Azure Functions 应用以及 Front Door 配置文件。  |
| [具有专用链接的 Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | 创建具有专用终结点的 Azure Functions 应用以及 Front Door 配置文件。  |
|**API 管理源**| **说明** |
| [API 管理（外部）](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | 创建具有外部 VNet 集成的 API 管理实例以及 Front Door 配置文件。  |
|**存储源**| **说明** |
| [存储静态网站](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | 创建具有公共终结点的 Azure 存储帐户和静态网站以及 Front Door 配置文件。  |
| [具有专用链接的存储 blob](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | 创建具有专用终结点的 Azure 存储帐户和 blob 容器以及 Front Door 配置文件。  |
|**应用程序网关源**| **说明** |
| [应用程序网关](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | 创建应用程序网关和 Front Door 配置文件。 |
|**虚拟机源**| **说明** |
| [具有专用链接服务的虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | 创建虚拟机和专用链接服务以及 Front Door 配置文件。 |
| | |
