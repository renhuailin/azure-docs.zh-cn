---
title: 在 Azure Sentinel 中保护安全托管服务提供商 (MSSP) 知识产权 |Microsoft Docs
description: 了解安全托管服务提供商 (MSSP) 如何保护他们在 Azure Sentinel 中创建的知识产权。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: da901a0beb5a90981293b4cae1f620a0668c7e0e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179375"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>在 Azure Sentinel 中保护 MSSP 知识产权

本文介绍了安全托管服务提供商 (MSSP) 可用于保护他们在 Azure Sentinel 中开发的知识产权的方法，如 Azure Sentinel 分析规则、搜寻查询、playbook 和工作簿。

无论你是作为[云解决方案提供商 (CSP)](#cloud-solutions-providers-csp)，还是客户具有[企业协议 (EA)/即用即付 (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) 帐户，你选择什么方法都将取决于每位客户购买 Azure 的方式。 以下各部分分别介绍了每种方法。

## <a name="cloud-solutions-providers-csp"></a>云解决方案提供商 (CSP)

如果作为云解决方案提供商 (CSP) 转售 Azure ，则需要管理客户的 Azure 订阅。 多亏了 [Admin-On-Behalf-Of (AOBO)](/partner-center/azure-plan-manage)，MSSP 租户的管理代理组中的用户可获得对客户的 Azure 订阅的所有者访问权限，并且默认情况下，客户不具有访问权限。

如果 MSSP 租户中的其他用户（非管理代理组成员）需要访问客户环境，建议你使用 [Azure Lighthouse](multiple-tenants-service-providers.md)。 使用 Azure Lighthouse，你可以使用某个内置角色向用户或组授予对特定范围（例如资源组或订阅）的访问权限。

如果需要为客户用户提供对 Azure 环境的访问权限，建议你在资源组（而不是整个订阅）级别向他们授予访问权限，这样可根据需要显示/隐藏环境的部分内容。

例如：

- 你可能会授予客户对其应用程序所在的多个资源组的访问权限，但仍需将 Azure Sentinel 工作区保存在客户无法访问的单独的资源组中。

- 使用此方法可以使客户能够查看选定的工作簿和 playbook，它们是可驻留在其各自资源组中的独立资源。

即使是在资源组级别向客户授予访问权限，客户将仍可以访问其原本可访问的资源的日志数据（例如来自 VM 的日志），甚至无需访问 Azure Sentinel。 有关详细信息，请参阅[按资源管理对 Azure Sentinel 数据的访问](resource-context-rbac.md)。

> [!TIP]
> 如果需要为客户提供对整个订阅的访问权限，请查看[企业协议 (EA)/即用即付 (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) 中的指南。
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Azure Sentinel CSP 体系结构示例

下图说明了向 CSP 客户提供访问权限时，[上一部分](#cloud-solutions-providers-csp)中所述的权限的工作原理：

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="与 CSP 客户一起保护 Azure Sentinel 知识产权。":::

在此图像中：

- 获得对 CSP 订阅的所有者访问权限的用户是 MSSP Azure AD 租户中管理代理组中的用户。
- MSSP 中的其他组通过 Azure Lighthouse 获取对客户环境的访问权限。
- 客户对 Azure 资源的访问权限由 Azure RBAC 在资源组级别进行管理。

    这允许 MSSP 根据需要隐藏 Azure Sentinel 组件，如分析规则和搜寻查询。

有关详细信息，请参阅 [Azure Lighthouse 文档](../lighthouse/concepts/cloud-solution-provider.md)。

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>企业协议 (EA)/即用即付 (PAYG)

如果客户可直接从 Microsoft 进行购买，则该客户已拥有对 Azure 环境的完全访问权限，并且你无法隐藏该客户的 Azure 订阅中的任何内容。

请根据需要保护的资源类型，按如下所示保护在 Azure Sentinel 中开发的知识产权：

### <a name="analytics-rules-and-hunting-queries"></a>分析规则和搜寻查询

分析规则和搜寻查询都包含在 Azure Sentinel 内，因此不能与 Azure Sentinel 工作区分离。

即使用户仅具有 Azure Sentinel 读取者权限，他们仍能够查看查询。 在这种情况下，建议你在自己的 MSSP 租户而不是客户租户中托管分析规则和搜寻查询。

为此，你需要使用自己的租户中启用了 Azure Sentinel 的工作区，还需要通过 [Azure Lighthouse](multiple-tenants-service-providers.md) 查看客户工作区。

若要在引用客户租户中的数据的 MSSP 租户中创建分析规则或搜寻查询，必须使用 `workspace` 语句，如下所示：

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

向分析规则添加 `workspace` 语句时，存在以下注意事项：

- **客户工作区中不应包含警报**。 通过这种方式创建的规则不会在客户工作区中创建警报或事件。 警报和事件只会存在于 MSSP 工作区中。

- **为每位客户创建单独的警报**。 使用此方法时，我们还建议为每位客户和每次检测使用单独的预警规则，因为工作区语句在每种情况下会有所不同。

    可以将客户名称添加到预警规则名称中，以便轻松识别触发警报的客户。 使用单独的警报可能会导致产生大量规则，你可能需要使用脚本或 [Azure Sentinel 作为代码](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)来管理这些规则。

    例如：

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="在 MSSP 工作区中为每位客户创建单独的规则。":::

- **为每位客户创建单独的 MSSP 工作区**。 为每位客户和每次检测创建单独的规则可能会达到工作区的最大分析规则数 (512)。 如果你有很多客户并预计将达到此限制，则可能需要为每位客户创建一个单独的 MSSP 工作区。

    例如：

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="在 MSSP 租户中为每位客户创建工作区和规则。":::

> [!IMPORTANT]
> 若要成功使用此方法，关键在于使用自动化来管理工作区中的众多规则。
>
> 有关详细信息，请参阅[跨工作区的分析规则](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>工作簿

如果你开发了一个 Azure Sentinel 工作簿，但不希望客户复制它，请将该工作簿托管在你的 MSSP 租户中。 确保你可以通过 Azure Lighthouse 访问客户工作区，然后确保修改工作簿以使用这些客户工作区。

例如：

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="跨工作区的工作簿":::

有关详细信息，请参阅[跨工作区的工作簿](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)。

如果希望客户能够查看工作簿可视化，同时仍将代码保密，则建议将工作簿导出到 Power BI。

将工作簿导出到 Power BI 可实现以下目的：

- **使工作簿可视化更易于共享**。 你可以向客户发送 Power BI 仪表板的链接，用户可以在其中查看报告的数据，而无需 Azure 访问权限。
- **启用计划**。 将 Power BI 配置为定期发送包含该时间段的仪表板快照的电子邮件。

有关详细信息，请参阅[将 Azure Monitor 日志数据导入 Power BI](../azure-monitor/visualize/powerbi.md)。

### <a name="playbooks"></a>攻略

可以按以下方式保护 playbook，具体取决于触发 playbook 的分析规则的创建位置：

- **在 MSSP 工作区中创建的分析规则**。  请确保在 MSSP 租户中创建 playbook，并从 MSSP 工作区获取所有事件和警报数据。 可在工作区中创建新规则时，请附加 playbook。

    例如：

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="在 MSSP 工作区中创建的规则。":::

- **在客户工作区中创建的分析规则**。 使用 Azure Lighthouse 将客户工作区的分析规则附加到 MSSP 工作区中托管的 playbook。 在这种情况下，playbook 将从客户工作区中获取警报和事件数据以及所有其他客户信息。

    例如：

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="在客户工作区中创建的规则。":::

在这两种情况下，如果 playbook 需要访问客户的 Azure 环境，请使用通过 Lighthouse 提供该访问权限的用户或服务主体。

但是，如果 playbook 需要访问客户租户中的非 Azure 资源（如 Azure AD、Office 365 或 Microsoft 365 Defender），则需要在客户租户中创建具有相应权限的服务主体，然后在 playbook 中添加该标识。

> [!NOTE]
> 如果将自动化规则与 playbook 结合使用，则必须在 playbook 所在的资源组上设置自动化规则权限。
> 有关详细信息，请参阅[运行 playbook 的自动化规则的权限](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks)。
>

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [适用于 MSSP 的 Azure Sentinel 技术性 Playbook](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [作为 MSSP 在 Azure Sentinel 中管理多个租户](multiple-tenants-service-providers.md)
- [跨工作区和租户扩展 Azure Sentinel](extend-sentinel-across-workspaces-tenants.md)
- [可视化和监视数据](monitor-your-data.md)
- [教程：在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)