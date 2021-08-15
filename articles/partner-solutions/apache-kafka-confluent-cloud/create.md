---
title: 通过 Azure 门户创建 Apache Kafka for Confluent Cloud - Azure 合作伙伴解决方案
description: 本文介绍如何使用 Azure 门户创建 Apache Kafka for Confluent Cloud 实例。
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9d2326d330dca9111a68edcc81c8c45f7525c1b0
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112521031"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-portal"></a>快速入门：Apache Kafka for Confluent Cloud 入门 - Azure 门户

在本快速入门中，你将使用 Azure 门户创建 Apache Kafka for Confluent Cloud 实例。

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

1. 如果在上一部分中选择了专用产品/服务，则有两个计划类型选项：

    - Confluent Cloud - 即用即付
    - 承诺 - 承诺计划

   如果未选择专用产品/服务，则只能选择即用即付。

   选择要使用的计划，然后选择“设置 + 订阅”。

    :::image type="content" source="media/setup-subscribe.png" alt-text="设置和订阅。":::

1. 在“创建 Confluent Cloud 资源”基础信息页上，输入以下值。 完成后，选择“下一步:**标记”** 。

    :::image type="content" source="media/setup-basics.png" alt-text="用于设置 Confluent Cloud 资源的窗体。":::

    | 属性 | 说明 |
    | ---- | ---- |
    | **订阅** | 在下拉菜单中，选择要部署到的 Azure 订阅。 必须具有所有者或参与者访问权限 。 |
    | **资源组** | 指定是要创建新的资源组还是使用现有资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../../azure-resource-manager/management/overview.md)。 |
    | **Confluent 组织名称** | 命名软件即服务 (SaaS) 资源。 |
    | **区域** | 从下拉菜单中选择以下区域之一： <br/><br/> 澳大利亚东部、加拿大中部、美国中部、美国东部、美国东部 2、法国中部、北欧、东南亚、英国南部、美国中西部、西欧、西欧 2 |
    | **规划** | 选择“即用即付”或“承诺” 。 |
    | **计费期限** | 基于所选计费计划预先填充。 |
    | **价格** | 基于所选 Confluent 计划预先填充。 |

1. 在“标签”上，为要应用于资源的标签输入名称和值对  。 输入标签后，选择“查看 + 创建”。

    :::image type="content" source="media/setup-tags.png" alt-text="添加项目标签。":::

1. 查看提供的设置。 准备就绪后，选择“创建”。

1. 创建资源需要几分钟时间。 可以在“通知”中查看部署状态。 部署完成后，选择资源以查看“概述”页。

    :::image type="content" source="media/deployment-status.png" alt-text="部署状态。":::

   如果遇到错误，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理 Confluent Cloud 资源](manage.md)
