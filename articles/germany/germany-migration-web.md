---
title: 将 Azure Web 资源从 Azure 德国迁移到全局 Azure
description: 本文介绍如何将 Azure Web 资源从 Azure 德国迁移到全局 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029174"
---
# <a name="migrate-web-resources-to-global-azure"></a>将 Web 资源迁移到全局 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助用户将 Azure Web 资源从 Azure 德国迁移到全局 Azure。

## <a name="web-apps"></a>Web 应用

目前不支持将使用 Azure 应用服务的 Web 应用功能创建的应用从 Azure 德国迁移到全局 Azure。 建议将 Web 应用导出为 Azure 资源管理器模板。 然后，在将位置属性更改为新的目标区域之后再重新部署。

> [!IMPORTANT]
> 更改位置、Azure Key Vault 机密、证书和其他 GUID，以与新区域保持一致。

### <a name="migrate-web-app-resource"></a>迁移 Web 应用资源

1. 从 Azure 德国订阅[将 Web 应用和应用服务计划导出为模板](../azure-resource-manager/templates/export-template-portal.md)。 在 Web 应用资源组中选择要迁移的资源，并将其导出为模板。
1. 将该模板下载为 zip 文件。 
1. 将 template.json 文件中的位置属性编辑为目标 Azure 全局区域。 例如，以下 JSON 文件的目标位置为“美国西部”。

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. 将修改的模板重新部署到 Azure 全局。 例如，可以使用 PowerShell 进行部署。

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>迁移 Web 应用内容

1. 在 Azure 德国门户中，选择 Web 应用。
1. 选择“开发工具”>“高级工具”。
1. 从顶部菜单中，选择“调试控制台”，然后选择“PowerShell” 。
1. 选择站点。
1. 选择 wwwroot 文件夹旁边的下载图标 。 下载的 zip 文件包含 Web 应用的源代码。
1. 将 Web 根部署到迁移的 Azure 全局 Web 应用。 例如，可以使用以下 PowerShell 脚本。

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

参考信息：

- 通过完成[应用服务教程](../app-service/tutorial-dotnetcore-sqldb-app.md)，学习更多新知识。
- 获取有关如何[导出 Azure 资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)的信息。
- 请查看 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
- 请查看[应用服务概述](../app-service/overview.md)。
- 获取 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="notification-hubs"></a>通知中心

若要将设置从一个 Azure 通知中心实例迁移到另一个实例，请先导出再导入所有注册令牌及其标记：

1. [导出现有的通知中心注册](/previous-versions/azure/azure-services/dn790624(v=azure.100))到 Azure Blob 存储容器。
1. 在目标环境中创建一个新的通知中心。
1. 从 Blob 存储向新的通知中心[导入注册令牌](/previous-versions/azure/azure-services/dn790624(v=azure.100))。

参考信息：

- 通过完成[通知中心教程](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)，学习更多新知识。
- 请查看[通知中心概述](../notification-hubs/notification-hubs-push-notification-overview.md)。

## <a name="event-hubs"></a>事件中心

若要迁移 Azure 事件中心，请从 Azure 德国导出事件中心资源模板，然后将该模板部署到全局 Azure。

1. 从 Azure 德国订阅[将事件中心导出为模板](../azure-resource-manager/templates/export-template-portal.md)。
1. [将事件中心模板作为自定义模板部署](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)到全局 Azure 订阅。 加载和部署从 Azure 德国订阅导出的模板。

参考信息：

- 请查看[事件中心概述](../event-hubs/event-hubs-about.md)。
- 请查看 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
- 获取有关如何[导出 Azure 资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)的信息。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="next-steps"></a>后续步骤

了解有关迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)