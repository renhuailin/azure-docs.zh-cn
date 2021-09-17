---
title: Azure Web 应用程序防火墙和 Azure Policy
description: Azure Web 应用程序防火墙 (WAF) 与 Azure Policy 结合，可帮助强制实施组织标准并大规模评估 WAF 资源的符合性
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7738976cbc3f7b87862cab73a5b5f52c6d499d47
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606110"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web 应用程序防火墙和 Azure Policy

Azure Web 应用程序防火墙 (WAF) 与 Azure Policy 结合，可帮助强制实施组织标准并大规模评估 WAF 资源的符合性。 Azure Policy 是一款治理工具，它提供一个聚合视图来评估环境的整体状态，并允许用户以按资源、按策略的粒度向下钻取。 Azure Policy 还可以对现有资源执行批量修正，以及对新资源自动修正，从而帮助资源符合规范。

## <a name="azure-policy-for-web-application-firewall"></a>适用于 Web 应用程序防火墙的 Azure Policy

Azure Policy 内置多种用于管理 WAF 资源的定义。 策略定义细分及其功能情况如下所示：

1. 应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)：如果在创建资源时存在 WAF，则会评估 Azure Front Door 服务。 策略定义具有三种效果：审核、拒绝和禁用。 审核跟踪 Azure Front Door 服务未附加 WAF 的情况，并允许用户查看 Azure Front Door 服务不符合规范的方面。 如果未附加 WAF，则“拒绝”会阻止创建任何 Azure Front Door 服务。 “已禁用”会关闭策略分配。

2. 应为应用程序网关启用 Web 应用程序防火墙 (WAF)：如果在创建资源时存在 WAF，将对应用程序网关进行评估。 策略定义具有三种效果：审核、拒绝和禁用。 审核跟踪应用程序网关未附加 WAF 的情况，并允许用户查看应用程序网关不符合规范的方面。 如果未附加 WAF，则“拒绝”会阻止创建任何应用程序网关。 “已禁用”会关闭策略分配。

3. Web 应用程序防火墙 (WAF) 应使用指定的 Azure Front Door 服务模式：要求 Azure Front Door 服务的所有 Web 应用程序防火墙策略都启用“检测”或“防护”模式。 策略定义具有三种效果：审核、拒绝和禁用。 审核跟踪 WAF 不符合指定模式的情况。 如果未处于正确模式，“拒绝”将阻止创建任何 WAF。 “已禁用”会关闭策略分配。

4. Web 应用程序防火墙 (WAF) 应使用指定的应用程序网关模式：要求应用程序网关的所有 Web 应用程序防火墙策略都启用“检测”检或“防护”模式。 策略定义具有三种效果：审核、拒绝和禁用。 审核跟踪 WAF 不符合指定模式的情况。 如果未处于正确模式，“拒绝”将阻止创建任何 WAF。 “已禁用”会关闭策略分配。

## <a name="launch-an-azure-policy"></a>启动 Azure Policy

1.  在 Azure 主页上，在搜索栏中键入“Plicy”，然后单击“Azure Policy”图标

2.  在“Azure Policy”服务的“创作”，选择“分配”。

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Azure Policy 中的“分配”选项卡":::

3.  在“分配”页上，选择顶部的“分配策略”图标。

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="“分配策略”页上的“基本”选项卡":::

4.  在“分配策略”页的“基本”选项卡上，更新以下字段：
    1.  范围：选择该策略定义应会影响哪些 Azure 订阅和资源组。
    2.  排除：从策略分配范围中选择要排除的任何资源。
    3.  策略定义：选择要应用于具有排除项的范围的策略定义。 在搜索栏中键入“Web 应用程序防火墙”，以选择相关的 Web 应用程序防火墙 Azure Policy。

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="显示“可用定义”页上“策略定义”选项卡的屏幕截图。":::

5.  选择“参数”选项卡，并更新策略分配参数。 要进一步了解参数的功能，请将鼠标悬停在参数名称旁边的信息图标上，以获取进一步说明。

6.  选择“查看 + 创建”以最终确定策略分配。 策略分配大约需要 15 分钟，才会对新资源生效。
