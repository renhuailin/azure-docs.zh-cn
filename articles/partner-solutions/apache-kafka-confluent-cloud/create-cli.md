---
title: 通过 Azure CLI 创建 Apache Kafka for Confluent Cloud - Azure 合作伙伴解决方案
description: 本文介绍如何使用 Azure CLI 创建 Apache Kafka for Confluent Cloud 实例。
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a9c49b1a19cbed080255492b90554ce0b138c3bd
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534668"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-cli"></a>快速入门：Apache Kafka for Confluent Cloud 入门 - Azure CLI

在本快速入门中，你将使用 Azure 市场和 Azure CLI 创建 Apache Kafka for Confluent Cloud 实例。

## <a name="prerequisites"></a>必备条件

- 一个 Azure 帐户。 如果没有活动 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 必须具有 Azure 订阅的所有者或参与者角色 。 Azure 和 Confluent 之间的集成只能由具有所有者或参与者访问权限的用户设置 。 在开始前，请[确认是否具有适当的访问权限](../../role-based-access-control/check-access.md)。

## <a name="find-offer"></a>查找产品/服务

使用 Azure 门户查找 Apache Kafka for Confluent Cloud 应用程序。

1. 在 Web 浏览器中转到 [Azure 门户](https://portal.azure.com/)，然后登录。

1. 如果最近访问过“市场”，请从可用选项中选择该图标。 否则，请搜索“市场”。

    :::image type="content" source="media/marketplace.png" alt-text="市场图标。":::

1. 在“市场”页上，根据所需计划的类型，有两个选项可供选择。 你可以注册即用即付计划或承诺计划。 “即用即付”向用户公开提供。 承诺计划面向已获批使用专用产品/服务的客户。

   - 对于即用即付客户，请搜索“Apache Kafka on Confluent Cloud”。 选择 Apache Kafka on Confluent Cloud 的产品/服务。

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="搜索 Azure 市场产品/服务。":::

   - 对于承诺客户，请选择“查看专用产品/服务”链接 。 “承诺”要求注册最低使用量。 请仅在需要长时间使用服务时才使用此选项。

     :::image type="content" source="media/view-private-offers.png" alt-text="查看专用产品/服务。":::

     查找 Apache Kafka on Confluent Cloud。

     :::image type="content" source="media/select-from-private-offers.png" alt-text="选择专用产品/服务。":::

## <a name="create-resource"></a>创建资源

选择 Apache Kafka on Confluent Cloud 的产品/服务后，即可设置应用程序。

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

登录后，使用 [az confluent organization create](/cli/azure/confluent/organization#az_confluent_organization_create) 命令创建新组织资源：

```azurecli
az confluent organization create --name "myOrganization" --resource-group "myResourceGroup" \
 --location "my location" \ 
 --offer-detail id="string" plan-id="string" plan-name="string" publisher-id="string" term-unit="string" \ 
 --user-detail email-address="contoso@microsoft.com" first-name="string" last-name="string" \ 
 --tags Environment="Dev" 
```

> [!NOTE]
> 如果要在创建操作完成前返回命令，请添加可选参数 `--no-wait`。 操作将继续运行，直到 Confluent 组织创建完成。
 
若要在组织的特定事件或条件发生前暂停 CLI 执行，请使用 [az confluent organization wait](/cli/azure/confluent/organization#az_confluent_organization_wait) 命令。 例如，等到组织创建完成后：

```azurecli
az confluent organization wait --name "myOrganization" --resource-group "myResourceGroup" --created
```

若要查看现有组织的列表，请使用 [az confluent organization list](/cli/azure/confluent/organization#az_confluent_organization_list) 命令。

可查看订阅中的所有组织：

```azurecli
az confluent organization list
```

或者，查看资源组中的组织：

```azurecli
az confluent organization list --resource-group "myResourceGroup"
```

若要查看特定组织的属性，请使用 [az confluent organization show](/cli/azure/confluent/organization#az_confluent_organization_show) 命令。

可按名称查看组织：

```azurecli
az confluent organization show --name "myOrganization" --resource-group "myResourceGroup"
```

或者，按资源 ID 查看组织：

```azurecli
az confluent organization show --ids "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

如果遇到错误，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理 Confluent Cloud 资源](manage.md)
