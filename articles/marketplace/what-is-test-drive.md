---
title: 什么是 Microsoft AppSource 中的体验版？
description: Microsoft AppSource 体验版功能的说明。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 12/08/2020
ms.openlocfilehash: 5fba64beff10f59be42776cc87cf6f37922295dd
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111949066"
---
# <a name="what-is-a-test-drive"></a>什么是体验版？

体验版是一种向潜在客户展示你的产品/服务的好方法，让他们可以选择“先试后买”，这样就可以开发出优质的潜在客户，提高转化率。 体验版可以让客户在实际场景中体验你的产品。 试用你的产品的客户显然有意购买类似的解决方案。 你可以通过此方式对更高级的潜在顾客进行跟进，这对你是有好处的。

你的客户同样也受益于体验版。 通过让客户先试用你的产品，可以减少购买过程中的摩擦。 此外，体验版是预配好的。也就是说，客户无需下载、安装或配置产品。

## <a name="how-does-it-work"></a>它是如何工作的？

体验版是托管实例，可按需为提出请求的客户启动你的解决方案或应用程序。 在分配体验版实例后，该客户就可以在设定的时间段内使用它。 该时间段结束后，它会被删除，为其他客户腾出空间。

作为发布者，你可以在合作伙伴中心管理和配置体验版设置。 技术配置详细信息因产品/服务类型而异。 有关详细指南，请参阅[体验版技术配置](./test-drive-technical-configuration.md)。

潜在客户可以在 [AppSource](https://appsource.microsoft.com/en-US/) 上你的产品/服务中发现 CTA 形式的体验版。 客户提供联系信息并同意产品/服务的条款和隐私策略后，就获得对预配置环境的访问权限，可以在固定时段内试用。 客户在试用过程中以自我引导的方式亲自体验你的产品的主要功能和优点，你将获得一个有价值的潜在顾客。

## <a name="types-of-test-drives"></a>体验版的类型

商业市场提供所选产品/服务的不同体验版，具体取决于产品类型、方案和市场：

- Azure 资源管理器
    - Azure 应用程序
    - SaaS
    - 虚拟机
- 托管的体验版
    - Dynamics 365 for Business Central（目前不受支持）
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 逻辑应用（只有支持模式）
- Power BI

若要详细了解如何配置其中的一个体验版，请参阅[体验版技术配置](./test-drive-technical-configuration.md)。 

### <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器体验版

此部署模板包含组成你的解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 Azure 资源管理器体验版适用于以下产品/服务类型： 

- Azure 应用程序
- SaaS
- 虚拟机

>[!NOTE]
>这是虚拟机和 Azure 应用程序产品/服务的唯一体验版选项。

### <a name="hosted-test-drive-recommended"></a>托管的体验版（建议）

托管的体验版由 Microsoft 托管并维护用于执行体验版用户预配和取消预配的服务，不需复杂的设置。 如果你在 Microsoft AppSource 上有产品/服务，请构建你的体验版以连接 Dynamics AX/CRM 实例。 你可以使用以下 AppSource 产品/服务类型：

- 将 [Dynamics 365 for Customer Engagement 和 Power Apps](dynamics-365-customer-engage-offer-setup.md) 用于客户参与系统，例如销售、服务、项目服务和现场服务。
- 将 [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) 用于财务和运营企业资源规划系统，例如财务、运营、制造、供应链。

### <a name="logic-app-test-drive"></a>逻辑应用体验版

此类型的体验版不由 Microsoft 托管，它将 Azure 资源管理器 (ARM) 模板用于 Dynamics AX/CRM 产品/服务类型。 你需要运行 ARM 模板，以便在 Azure 订阅中创建所需的资源。 逻辑应用体验版当前只有支持模式，Microsoft 不建议使用它。若要详细了解如何配置逻辑应用体验版，请参阅[体验版技术配置](./test-drive-technical-configuration.md)。

### <a name="power-bi-test-drive"></a>Power BI 体验版

这只是一个指向定制仪表板的嵌入链接。 仅演示交互式 Power BI 视觉对象的任何产品都应使用此类型的体验版。

## <a name="transforming-examples"></a>转换示例

将资源的体系结构转换为体验版的过程可能会让人望而却步。 请查看这些示例，了解如何以最佳方式转换当前体系结构。

[将网站模板转换为体验版](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[将虚拟机模板转换为体验版](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[将现有的资源管理器模板转换为体验版](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>从体验版产生潜在顾客

商业市场体验版对于营销人员来说是一个很棒的工具。 建议你在启动时将其纳入市场推广工作中，以便为业务带来更多潜在顾客。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)。

如果你与体验版的潜在顾客达成了交易，请务必在 [Microsoft 合作伙伴销售联络系统](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)中登记该交易。 另外，我们很乐意听到体验版在客户的成功中发挥了作用。

## <a name="other-resources"></a>其他资源

其他体验版资源：

- [体验版最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）

## <a name="next-steps"></a>后续步骤

- [体验版技术配置](test-drive-technical-configuration.md)