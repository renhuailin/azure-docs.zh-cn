---
title: 将 Azure 集成资源从 Azure 德国迁移到全球 Azure
description: 本文提供将 Azure 集成资源从 Azure 德国迁移到全球 Azure 的相关信息。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0a4ea6393741e15d8dce94869213c62c08537c7e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "117028997"
---
# <a name="migrate-integration-resources-to-global-azure"></a>将集成资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文提供可帮助你将 Azure 集成资源从 Azure 德国迁移到全球 Azure 的相关信息。

## <a name="service-bus"></a>服务总线

Azure 服务总线服务没有数据导出或导入功能。 若要将服务总线资源从 Azure 德国迁移到全球 Azure，可以将资源[作为 Azure 资源管理器模板导出](../azure-resource-manager/templates/export-template-portal.md)。 接着，针对全球 Azure 调整导出的模板，然后重新创建资源。

> [!NOTE]
> 导出资源管理器模板不会复制数据（例如消息）。 导出模板只会重新创建元数据。

> [!IMPORTANT]
> 更改位置、Azure Key Vault 机密、证书和其他 GUID 以与新区域保持一致。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>服务总线元数据

导出资源管理器模板时，会重新创建以下服务总线元数据元素：

- 命名空间
- 队列
- 主题
- 订阅
- 规则
- 授权规则

### <a name="keys"></a>键

前面导出和重新创建的步骤不会复制与授权规则关联的共享访问签名密钥。 如果需要保留共享访问签名密钥，请使用带有可选参数 `-Keyvalue` 的 `New-AzServiceBuskey` cmdlet 以字符串的形式接受密钥。 [Azure PowerShell Az 模块](/powershell/azure/install-az-ps)中提供有此更新的 cmdlet。

### <a name="usage-example"></a>用例

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> 即使保留密钥，也必须更新应用程序才能使用新的连接字符串。 DNS 主机名在 Azure 德国和全球 Azure 中是不同的。

### <a name="sample-connection-strings"></a>示例连接字符串

**Azure 德国**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**全球 Azure**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

参考信息：

- 完成[服务总线教程](../service-bus-messaging/index.yml)，学习更多新知识。
- 熟悉如何[导出资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。
- 查看[服务总线概述](../service-bus-messaging/service-bus-messaging-overview.md)。

## <a name="logic-apps"></a>逻辑应用

Azure 逻辑应用在 Azure 德国不可用，但你可以改为在全球 Azure 中使用逻辑应用来创建计划作业。 Azure 计划程序之前在 Azure 德国中可用，但现在已经停用了。

参考信息：

- 通过完成 [Azure 逻辑应用教程](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)了解更多信息。
- 查看 [Azure 逻辑应用概述](../logic-apps/logic-apps-overview.md)。

## <a name="next-steps"></a>后续步骤

了解适用于迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)
