---
title: 创建 Logz.io 资源 - Azure 合作伙伴解决方案
description: 介绍如何在 Azure 中创建 Logz.io 资源的快速入门文章。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 55b5057542af2812a6d5c21acf3d4a9e1f64c293
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429327"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>快速入门：在 Azure 门户中创建 Logz.io 资源

本文介绍如何启用 Azure 的 Logz.io 软件即服务 (SaaS) 集成。 使用 Logz.io 监视 Azure 环境的运行状况和性能。

## <a name="prerequisites"></a>必备条件

- 订阅所有者：若要设置 Logz.io，必须在 Azure 订阅中为你分配[所有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 开始此集成之前，请[检查你的访问权限](../../role-based-access-control/check-access.md)。
- 注册资源提供程序 - 如果尚未为订阅注册 `Microsoft.Insights` ，则注册该资源提供程序。 有关详细信息，请参阅[注册资源提供程序](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

## <a name="find-offer"></a>查找产品/服务

使用 Azure 门户在 Azure Marketplace 中查找 Logz.io。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 如果最近访问过“市场”，请从可用选项中选择该图标。 否则，请搜索“市场”。

    :::image type="content" source="./media/create/marketplace.png" alt-text="Azure 市场。":::

1. 在 Azure 市场中，请搜索“Logz.io”。
1. 选择“Logz.io - 可观察性(基于 ELK 和 Prometheus)”。
1. 选择“设置 + 订阅”按钮。 此时将打开“创建新的 Logz.io 帐户”窗口。

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Logz.io 可观察性应用程序。":::

## <a name="create-new-logzio-account"></a>创建新的 Logz.io 帐户

在“创建 Logz.io 帐户”屏幕的“基本信息” 选项卡上，输入以下值：

| 属性 | 描述 |
| ---- | ---- |
| **订阅** | 从下拉菜单中，选择你对其具有所有者访问权限的 Azure 订阅。 |
| **资源组** | 指定是要创建新的资源组还是使用现有资源组。 “资源组”是用于保存 Azure 解决方案相关资源的容器。 |
| Logz 帐户名称 | 为要创建的 Logz.io 帐户提供名称。 |
| **位置** | 选择“美国西部 2”或“欧洲西部”。 在预览期间，Logz.io 仅支持这些 Azure 区域。 |
| 定价计划 | 从可用 Logz.io 计划列表中选择。 |
| 计费期限 | 默认值为“每月”。 |
| **价格** | 基于选定的 Logz.io 计划指定。 |

:::image type="content" source="./media/create/basics.png" alt-text="创建 Logz.io 帐户。":::

输入值后，选择“下一步: 日志和指标”按钮。

## <a name="configure-logs"></a>配置日志

在“日志和指标”选项卡上，指定要将日志和指标发送到 Logz.io 的 Azure 资源。

可将两种类型的日志从 Azure 发送到 Logz.io：

- 订阅级别日志：提供外部（管理平面）订阅中针对各个 Azure 资源操作的见解和有关服务运行状况事件的更新。 使用活动日志，可以确定对订阅中的资源执行任何写入操作（GET、POST、DELETE）的内容、用户和时间。 每个 Azure 订阅都具有单个活动日志。
- Azure 资源日志 - 提供对 Azure 资源（数据平面）内运行的操作的见解。 例如，从密钥保管库获取密钥或向数据库发出请求。 资源日志的内容因 Azure 服务和资源类型而异。

通过选中标题为“发送订阅级别日志”的框，可以将订阅级别日志发送到 Logz.io。 如果未选中此框，则不会将任何订阅级别日志发送到 Logz.io。

通过选中标题为“为所有已定义资源发送 Azure 资源日志”的框，可以将 Azure 资源日志发送到 Logz.io。 Azure 资源日志的类型在 [Azure Monitor 资源日志类别](../../azure-monitor/essentials/resource-logs-categories.md)中列出。 若要筛选向 Logz.io 发送日志的特定 Azure 资源集，可以使用 Azure 资源标记。

发送日志的标记规则如下：

- 默认情况下，将收集所有资源的日志。
- 带有 Include 标记的 Azure 资源会将日志发送到 Logz.io。
- 带有 Exclude 标记的 Azure 资源不会将日志发送到 Logz.io。
- 如果包含规则与排除规则之间存在冲突，则排除规则优先。

:::image type="content" source="./media/create/logs.png" alt-text="设置日志和指标。":::

配置日志和指标之后，选择“下一步: 标记”按钮。

## <a name="add-custom-tags"></a>添加自定义标记

可以通过添加键值对，为新的 Logz.io 资源指定自定义标记。

每个键值对应包含“名称”和“值”：

| 属性 | 说明 |
| ---- | ---- |
| **名称** | 对应于 Azure Logz.io 资源的标记的名称。 |
| 值 | 对应于 Azure Logz.io 资源的标记的值。 |

:::image type="content" source="./media/create/tags.png" alt-text="添加自定义标记。":::

添加选项卡后，选择“下一步: 单一登录”按钮。

## <a name="configure-single-sign-on"></a>配置单一登录

单一登录 (SSO) 是一项可选功能：

- 若要选择退出 SSO，请跳过此步骤。
- 若要选择加入 SSO，请参阅[设置 Logz.io 单一登录](setup-sso.md)。

配置 AAD 后，请从“单一登录”选项卡中选择 Logz.io SSO 应用程序。

:::image type="content" source="./media/create/sso.png" alt-text="配置单一登录。":::

选择“下一步: 审阅 + 创建”按钮，导航到资源创建的最后一步。

## <a name="create-resource"></a>创建资源

在“审阅 + 创建”页面上运行所有验证。 可以查看在“基本”、“日志和指标”、“标记”和“单一登录”   选项卡中所做的所有选择。 还可以查看 Logz.io 和 Azure 市场条款和条件。

查看所有信息，验证其是否正确。 若要开始部署，请选择“创建”按钮。

:::image type="content" source="./media/create/create-resource.png" alt-text="查看并创建帐户。":::

成功部署后，可通过选择“转到资源”按钮查看已部署的 Logz.io 资源。

## <a name="next-steps"></a>后续步骤

- 了解如何[管理](manage.md) Logz.io 集成。
- 若要解决集成问题，请参阅[故障排除](troubleshoot.md)。
