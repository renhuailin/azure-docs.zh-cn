---
title: 如何在 Azure Spring Cloud 中使用持久性存储 | Microsoft Docs
description: 如何在 Azure Spring Cloud 中使用持久性存储
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 925f1d8113f08f1bc8b063623dcc85ac79964047
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015511"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中使用永久性存储

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 为应用程序提供两种类型的存储：持久性和临时性。

默认情况下，Azure Spring Cloud 为每个应用程序实例提供临时存储。 每个实例的临时存储有 5 GB 的容量限制，其默认安装路径为 /tmp。

> [!WARNING]
> 重启应用程序实例会永久删除其关联的临时存储。

持久存储是由 Azure 托管的文件共享容器，按应用程序分配。 存储在持久存储中的数据由应用程序的所有实例共享。 一个 Azure Spring Cloud 实例最多有 10 个启用了持久存储的应用程序。 为每个应用程序分配 50 GB 的持久存储。 持久存储的默认装载路径为 /persistent。

> [!WARNING]
> 如果禁用应用程序的持久存储，则所有存储都将解除分配，并且存储的所有数据都将丢失。

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>使用 Azure 门户启用持久存储

1. 在 Azure 门户的“主页”页上，选择“所有资源” 。

    >![找到“所有资源”图标](media/portal-all-resources.jpg)

1. 选择需要持久存储的 Azure Spring Cloud 资源。 在此示例中，所选应用程序名为 upspring。

    > ![选择应用程序](media/select-service.jpg)

1. 在“设置”标题下，选择“应用” 。

1. Azure Spring Cloud 服务随即显示在表中。  选择要向其添加持久存储的服务。 本例选择了“网关”服务。

    > ![选择服务](media/select-gateway.jpg)

1. 在服务的配置页上选择“配置”

1. 选择“持久存储”选项卡并选择“启用” 。

    > ![启用持久存储](media/enable-persistent-storage.jpg)

启用持久存储后，其大小和路径将显示在配置页上。

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>使用 Azure CLI 修改持久存储

如有必要，请安装用于 Azure CLI 的 Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```

其他操作：

* 创建启用了持久存储的应用：

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 为现有应用启用持久存储：

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 在现有应用中禁用持久存储：

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > 如果禁用应用程序的持久存储，则所有存储都将解除分配，并且存储的所有数据都将永久丢失。

## <a name="next-steps"></a>后续步骤

* 了解[应用程序和服务配额](./quotas.md)。
* 了解如何[手动缩放应用程序](./how-to-scale-manual.md)。
