---
title: 创建 Datadog - Azure 合作伙伴解决方案
description: 本文介绍如何使用 Azure 门户创建 Datadog 的实例。
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 6b2737877ecc550540761bfb704aee98f6e7c2f4
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110655222"
---
# <a name="quickstart-get-started-with-datadog-by-creating-new-instance"></a>快速入门：通过创建新实例开始使用 Datadog

在本快速入门中，你将创建一个新 Datadog 实例。 可以创建新的 Datadog 组织，也可以[链接到现有 Datadog 组织](link-to-existing-organization.md)。

## <a name="prerequisites"></a>先决条件

在 Azure 中创建第一个 Datadog 实例之前，请[配置环境](prerequisites.md)。 必须先完成这些步骤，然后才能继续执行本快速入门中的后续步骤。

## <a name="find-offer"></a>查找产品/服务

使用 Azure 门户找到 Datadog。

1. 转到 [Azure 门户](https://portal.azure.com/)并登录。

1. 如果最近访问过“市场”，请从可用选项中选择该图标。 否则，请搜索“市场”。

    :::image type="content" source="media/create/marketplace.png" alt-text="市场图标。":::

1. 在市场中搜索 **Datadog**。

1. 在“计划概述”屏幕中，选择“设置 + 订阅”。

   :::image type="content" source="media/create/datadog-app-2.png" alt-text="Azure 市场中的 Datadog 应用程序。":::

## <a name="create-a-datadog-resource-in-azure"></a>在 Azure 中创建 Datadog 资源

门户会显示一个选项，询问你是要创建 Datadog 组织，还是将 Azure 订阅链接到现有 Datadog 组织。

若要新建 Datadog 组织，请选择“创建新的 Datadog 组织”下的“创建”

:::image type="content" source="media/create/datadog-create-link-selection.png" alt-text="创建或链接 Datadog 组织" border="true":::

门户将显示一个用于创建 Datadog 资源的表单。

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="创建 Datadog 资源" border="true":::

提供以下值。

|属性 | 说明
|:-----------|:-------- |
| 订阅 | 选择要用于创建 Datadog 资源的 Azure 订阅。 你必须拥有所有者访问权限。 |
| 资源组 | 指定是要创建新的资源组还是使用现有的资源组。 “资源组”是用于保存 Azure 解决方案相关资源的容器。 |
| 资源名称 | 指定 Datadog 资源的名称。 创建新的 Datadog 组织时，此名称将是新 Datadog 组织的名称。 |
| 位置 | 选择“美国西部 2”。 目前，“美国西部 2”是唯一受支持的区域。 |
| 定价计划 | 创建新组织时，请从可用 Datadog 计划列表中进行选择。 |
| 计费期限 | 每月。 |

## <a name="configure-metrics-and-logs"></a>配置指标和日志

使用 Azure 资源标记可配置要将哪些指标和日志发送到 Datadog。 可以包含或排除特定资源的指标和日志。

用于发送 **指标** 的标记规则为：

- 默认情况下，将收集除虚拟机、虚拟机规模集和应用服务计划以外的其他所有资源的指标。
- 带有 *Include* 标记的虚拟机、虚拟机规模集和应用服务计划会将指标发送到 Datadog。
- 带有 *Exclude* 标记的虚拟机、虚拟机规模集和应用服务计划不会将指标发送到 Datadog。
- 如果包含规则与排除规则之间存在冲突，则排除规则优先

有关发送 **日志** 的标记规则为：

- 默认情况下，将收集所有资源的日志。
- 带有 *Include* 标记的 Azure 资源会将日志发送到 Datadog。
- 带有 *Exclude* 标记的 Azure 资源不会将日志发送到 Datadog。
- 如果包含规则与排除规则之间存在冲突，排除规则优先。

例如，以下屏幕截图显示了一个标记规则。在使用该标记规则的情况下，只有标记为 *Datadog = True* 的那些虚拟机、虚拟机规模集和应用服务计划会将指标发送到 Datadog。

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="配置日志和指标。" border="true":::

可将两种类型的日志从 Azure 发送到 Datadog。

1. **订阅级日志** - 提供在 [控制平面](../../azure-resource-manager/management/control-plane-and-data-plane.md)对你的资源执行的操作的见解。 还包括有关服务运行状况事件的更新。 使用活动日志来确定任何写入操作（PUT、POST、DELETE）的内容、操作者和操作时间。 每个 Azure 订阅都具有单个活动日志。

1. **Azure 资源日志** - 提供在 [数据平面](../../azure-resource-manager/management/control-plane-and-data-plane.md)对 Azure 资源执行的操作的见解。 例如，从密钥保管库获取机密就是一个数据平面操作。 向数据库发出请求也是一个数据平面操作。 资源日志的内容因 Azure 服务和资源类型而异。

若要将订阅级日志发送到 Datadog，请选择“发送订阅活动日志”。 如果不选中此选项，则不会将任何订阅级日志发送到 Datadog。

若要将 Azure 资源日志发送到 Datadog，请选择“发送所有已定义的资源的 Azure 资源日志”。 Azure 资源日志的类型在 [Azure Monitor 资源日志类别](../../azure-monitor/essentials/resource-logs-categories.md)中列出。  若要筛选向 Datadog 发送日志的 Azure 资源集，请使用 Azure 资源标记。

发送到 Datadog 的日志将由 Azure 收费。 有关详细信息，请参阅发送到 Azure 市场合作伙伴的[平台日志的定价](https://azure.microsoft.com/pricing/details/monitor/)。

配置完指标和日志后，选择“下一步: 单一登录”。

## <a name="configure-single-sign-on"></a>配置单一登录

如果你的组织使用 Azure Active Directory 作为标识提供者，那么你可以建立从 Azure 门户到 Datadog 的单一登录。 如果你的组织使用其他标识提供者，或者你暂时不想要建立单一登录，则可以跳过本部分。

若要通过 Azure Active Directory 建立单一登录，请选中“通过 Azure Active Directory 启用单一登录”对应的复选框。

Azure 门户将从 Azure Active Directory 中检索相应的 Datadog 应用程序。 该应用与在前面步骤中提供的企业应用相匹配。

选择 Datadog 应用名称。

:::image type="content" source="media/create/sso.png" alt-text="启用到 Datadog 的单一登录。" border="true":::

在完成时选择“下一步:  标记”。

## <a name="add-custom-tags"></a>添加自定义标记

可为新的 Datadog 资源指定自定义标记。 提供要应用到 Datadog 资源的标记的名称和值对。

:::image type="content" source="media/create/tags.png" alt-text="为 Datadog 资源添加自定义标记。" border="true":::

添加完标记后，选择“下一步: 查看 + 创建”。

## <a name="review--create-datadog-resource"></a>查看并创建 Datadog 资源

查看所做的选择和使用条款。 验证完成后，选择“创建”。

:::image type="content" source="media/create/review-create.png" alt-text="查看并创建 Datadog 资源。" border="true":::

Azure 将部署该 Datadog 资源。

完成该过程后，选择“转到资源”以查看该 Datadog 资源。

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog 资源部署。" border="true":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理 Datadog 资源](manage.md)
