---
title: Bicep 示例
description: 查找某些常用应用服务方案的 Bicep 示例。 了解如何自动完成应用服务部署或管理任务。
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: de66f32afb86da526765016e9b8feca7d5ada337
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059092"
---
# <a name="bicep-files-for-app-service"></a>适用于应用服务的 Bicep 文件

下表包含适用于 Azure 应用服务的 Bicep 文件的链接。 有关 Bicep 的快速入门和详细信息，请参阅 [Bicep 文档](/azure/azure-resource-manager/bicep/)。

若要了解应用服务资源的 Bicep 语法和属性，请参阅 [Microsoft.Web 资源类型](/azure/templates/microsoft.web/allversions)。

| 部署应用 | 说明 |
|-|-|
| [应用服务计划和基本的 Linux 应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) | 部署为 Linux 配置的应用服务应用。 |
| [应用服务计划和基本的 Windows 应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) | 部署为 Windows 配置的应用服务应用。 |
| [具有容器的网站](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | 通过为 Linux 配置的 docker 映像部署网站。 |
| **配置应用** | **说明** |
| [具有条件日志记录功能的应用](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| 部署带有日志记录启用条件的应用服务应用。 |
| [具有日志分析模块的应用](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| 部署具有日志分析功能的应用服务应用。 |
| [具有区域 VNet 集成的应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| 部署已启用区域 VNet 集成的应用服务应用。 |
|**使用连接资源的应用**| **说明** |
| [具有 CosmosDB 的应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| 在 Linux 上部署使用 CosmosDB 的应用服务应用。 |
| [使用 MySQL 的应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql)| 在 Windows 上部署使用 Azure Database for MySQL 的应用服务应用。 |
| [使用 Azure SQL 数据库中的数据库的应用](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| 在“基本”服务级别部署应用服务应用和 Azure SQL 数据库中的数据库。 |
| [连接到后端 webapp 的应用](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| 将两个 Web 应用（前端和后端）与 VNet 注入和专用终结点安全地连接在一起。 |
| [具有数据库、托管标识和监视功能的应用](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| 部署具有数据库、托管标识和监视功能的应用服务应用。 |
|**应用服务环境**| **说明** |
| [创建应用服务环境 v2](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) | 在虚拟网络中创建应用服务环境 v2。 |
| | |