---
title: 将 Azure IoT 资源从 Azure 德国迁移到全局 Azure
description: 本文介绍如何将 Azure IoT 资源从 Azure 德国迁移到全局 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 6e480ef9c12bdf425a41f567f1941d3f1a654b08
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029176"
---
# <a name="migrate-iot-resources-to-global-azure"></a>将 IoT 资源迁移到全局 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助用户将 Azure IoT 资源从 Azure 德国迁移到全局 Azure。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

可以使用 Azure Cosmos DB 数据迁移工具将数据迁移到 Azure Cosmos DB。 Azure Cosmos DB 数据迁移工具是一种开源解决方案，可将数据从各种源导入 Azure Cosmos DB。

Azure Cosmos DB 数据迁移工具以图形界面工具或命令行工具的形式提供。 [Azure Cosmos DB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub 存储库中提供了源代码。 Microsoft 下载中心提供了[该工具的内部版本](https://www.microsoft.com/download/details.aspx?id=46436)。

若要迁移 Azure Cosmos DB 资源，建议完成以下步骤：

1. 查看应用程序运行时间要求和帐户配置，以确定最佳操作计划。
1. 运行数据迁移工具，将 Azure 德国中的帐户配置克隆到新区域。
1. 如果可以使用维护时段，请运行数据迁移工具以将源中的数据复制到目标。
1. 如果无法使用维护时段，请运行该工具以将源中的数据复制到目标，然后完成以下步骤：
   1. 使用配置驱动的方法对应用程序中的读取/写入进行更改。
   1. 完成首次同步。
   1. 设置增量同步并与更改源同步。
   1. 将读取指向新帐户，并验证应用程序。
   1. 停止写入旧帐户，验证是否已同步更改源，然后将写入指向新帐户。
   1. 停止该工具并删除旧帐户。
1. 运行该工具，以验证新旧帐户之间的数据是否一致。

参考信息：

- 阅读 [Azure Cosmos DB 简介](../cosmos-db/introduction.md)。
- 了解如何[将数据导入到 Azure Cosmos DB](../cosmos-db/import-data.md)。

## <a name="functions"></a>函数

目前不支持将 Azure Functions 资源从 Azure 德国迁移到全局 Azure。 建议先导出资源管理器模板，更改位置，然后再重新部署到目标区域。

> [!IMPORTANT]
> 更改位置、Azure Key Vault 机密、证书和其他 GUID，以与新区域保持一致。

参考信息：

- 通过完成 [Functions 教程](../azure-functions/index.yml)，学习更多新知识。
- 了解如何[导出资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。
- 查看 [Azure Functions 概述](../azure-functions/functions-overview.md)。
- 阅读 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="notification-hubs"></a>通知中心

若要将设置从一个 Azure 通知中心实例迁移到另一个实例，请先导出再导入所有注册令牌和标记：

1. [导出现有的通知中心注册](/previous-versions/azure/azure-services/dn790624(v=azure.100))到 Azure Blob 存储容器。
1. 在目标环境中创建一个新的通知中心。
1. 从 Blob 存储向新的通知中心[导入注册令牌](/previous-versions/azure/azure-services/dn790624(v=azure.100))。

参考信息：

- 通过完成[通知中心教程](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)，学习更多新知识。
- 请查看[通知中心概述](../notification-hubs/notification-hubs-push-notification-overview.md)。

## <a name="iot-hub"></a>IoT 中心

尽管可以将 Azure IoT 中心实例从 Azure 德国迁移到全局 Azure，但迁移并不是无缝的。

> [!NOTE]
> 这种迁移可能会导致 Azure IoT 应用出现停机和数据丢失。 所有遥测消息、C2D 命令和与作业相关的信息（日程安排和历史记录）都不会迁移。 必须重新配置设备和后端应用程序，才能开始使用新的连接字符串。

### <a name="step-1-re-create-the-iot-hub"></a>步骤1：重新创建 IoT 中心

IoT 中心不支持以本机方式克隆。 但是，可以使用 Azure 资源管理器功能[将资源组导出为模板](../azure-resource-manager/templates/export-template-portal.md)，以便导出 IoT 中心元数据。 已配置的路由和其他 IoT 中心设置包含在导出的元数据中。 然后，在全局 Azure 中重新部署该模板。 通过查看导出的 JSON 中的详细信息，可以更轻松地在 Azure 门户中重新创建 IoT 中心。

### <a name="step-2-migrate-device-identities"></a>步骤 2：迁移设备标识

迁移设备标识：

1. 在 Azure 德国的源租户中，使用 [ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) 资源管理器 API 将所有设备标识、设备孪生和模块孪生（包括密钥）导出到存储容器。 可以使用 Azure 德国或全局 Azure 中的存储容器。 请确保生成的共享访问签名 URI 具有足够的权限。 
1. 运行 [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) 资源管理器 API，将所有设备标识从存储容器导入到全局 Azure 中克隆的 IoT 中心。
1. 重新配置设备和后端应用程序，以便开始使用新的连接字符串。 主机名将从 \*.azure-devices.de 更改为 \*.azure-devices.com。  

> [!NOTE]
> Azure 德国和全局 Azure 中的根证书颁发机构不同。 在重新配置与 IoT 中心实例交互的设备和后端应用程序时，请注意这一点。

参考信息：

- 了解如何[导出 IoT 中心批量标识](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices)。
- 了解如何[导入 IoT 中心批量标识](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices)。
- 查看 [Azure IoT 中心概述](../iot-hub/about-iot-hub.md)。

## <a name="next-steps"></a>后续步骤

了解有关迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)