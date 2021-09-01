---
title: Azure 德国开发人员指南 | Microsoft Docs
description: 本文比较了多项功能，并提供 Azure 德国应用程序开发指南。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: e631c37123c375ac3005aceb6c51289f07d9a22d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029178"
---
# <a name="azure-germany-developer-guide"></a>Azure 德国开发人员指南

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure 德国环境是一个 Microsoft Azure 实例，独立于 Microsoft 网络的其余部分。 此指南介绍应用程序开发人员和管理员在以交互方式使用不同的 Azure 区域时必须了解的差异。

## <a name="overview"></a>概述
Microsoft 提供多种工具，帮助开发人员创建云应用程序并将其部署到全球 Microsoft Azure 服务（简称“全球 Azure”）和 Microsoft Azure 德国服务。 Azure 德国致力满足客户的安全和合规性需求，以遵守德国数据隐私法规。 Azure 德国提供与全球 Azure 部署的物理和网络隔离，以及根据德国法律执行的数据托管方。

开发人员在创建应用程序并将其部署到 Azure 德国而不是全球 Azure 时，需要了解这两组服务的区别。 需要了解的具体方面包括：设置和配置其编程环境、配置终结点、编写应用程序以及将应用程序作为服务部署到 Azure 德国。

本指南中的信息总结了这些差异， 并对 [Azure 德国](https://azure.microsoft.com/overview/clouds/germany/ "Azure 德国")站点和 [Azure 文档中心](https://azure.microsoft.com/documentation/)上提供的信息进行了补充。 

也可以从其他位置获取官方信息，例如：
* [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/ "Microsoft Azure 信任中心") 
* [Azure 博客](https://azure.microsoft.com/blog/ "Azure 博客")
* [Azure 德国博客](/archive/blogs/azuregermany/ "Azure 德国博客")

## <a name="guidance-for-developers"></a>开发人员指南
大多数当前可用的技术内容假定应用程序是针对全球 Azure 而不是 Azure 德国开发。 因此，必须了解为在 Azure 德国中托管而开发的应用程序的两个重要差异：

* 全球 Azure 特定区域中的某些服务和功能可能无法在 Azure 德国中使用。
* Azure 德国中的功能配置可能不同于全球 Azure 中的配置。 必须检查示例代码、配置和步骤，以确保是在 Azure 德国云服务环境中构建和执行。

目前，德国中部和东北部是 Azure 德国支持的区域。 如需了解区域和可用服务，请参阅[可用产品（按地区）](https://azure.microsoft.com/regions/services)。


## <a name="endpoint-mapping"></a>终结点映射
若要了解如何将全球 Azure 和 Azure SQL 数据库终结点映射到 Azure 德国特定终结点，请参阅下表：

| 名称 | Azure 德国终结点 |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | `https://management.core.cloudapi.de/` |
| GalleryUrl                               | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl                      | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl                     | `https://management.core.cloudapi.de/` |
| PublishSettingsFileUrl                   | `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl                       | `https://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix                     | `.database.cloudapi.de` |
| core.usgovcloudapi.net                    | `core.cloudapi.de` |
| ActiveDirectoryAuthority                 | `https://login.microsoftonline.de/` |
| GraphUrl                                 | `https://graph.cloudapi.de/` |
| TrafficManagerDnsSuffix                  | `azuretrafficmanager.de` |
| AzureKeyVaultDnsSuffix                   | `vault.microsoftazure.de` |
| AzureKeyVaultServiceEndpointResourceId   | `https://vault.microsoftazure.de` |
| 服务总线后缀                       | `servicebus.cloudapi.de` |


## <a name="next-steps"></a>后续步骤
有关 Azure 德国的详细信息，请参阅以下资源：

* [注册试用版](https://azure.microsoft.com/free/germany/)
* [获取 Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)
* [登录页面](https://portal.microsoftazure.de/)（如果已有 Azure 德国帐户）
* [Azure 德国概述](./germany-welcome.md)
* [Azure 德国博客](/archive/blogs/azuregermany/)
* [Azure 合规性](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)