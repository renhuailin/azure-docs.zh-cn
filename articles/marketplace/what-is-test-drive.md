---
title: 什么是体验版？ Microsoft 商业市场
description: Marketplace 测试驱动器功能的说明
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490034"
---
# <a name="what-is-a-test-drive"></a>什么是体验版？

测试驱动器是向潜在客户展示您的产品/服务的一种好方法，通过向客户提供产品/服务，让客户在购买前进行尝试，生成高度合格的潜在顾客并导致转换增加。 在实际实施方案中，测试驱动器将使你的产品进入现实。 试用你的产品的客户正打算清楚地购买类似的解决方案。 通过跟进更高级的潜在顾客来利用这一优势。

你的客户也受益于测试驱动器。 通过允许他们首先尝试您的产品，您可以减少购买过程的摩擦。 此外，还预配了测试驱动器，即客户无需下载、设置或配置产品。

## <a name="how-does-it-work"></a>工作原理

测试驱动器是按需为请求客户端的客户启动解决方案或应用程序的托管实例。 一旦分配了一个测试驱动器实例，该客户就可以在设定的时间段内使用它。 该时间段结束后，它将被删除，为另一个客户腾出空间。

作为发布者，你可以在合作伙伴中心管理和配置测试驱动器设置。 技术配置详细信息因产品类型而异。 有关详细指南，请参阅 [测试驱动器技术配置](./test-drive-technical-configuration.md)。

潜在客户在 [AppSource](https://appsource.microsoft.com/en-US/)上发现你的产品/服务的 CTA。 它们提供了其联系信息并同意你的产品/服务的条款和隐私策略，并获得预配置环境的访问权限，以便在一段固定的时间内试用。 客户可获得对你的产品关键功能和权益的动手、自行指导的试用版，你将获得宝贵的潜在顾客。

## <a name="types-of-test-drives"></a>测试驱动器的类型

选择产品/服务的商用 marketplace 提供有不同的测试驱动器，具体取决于所使用的产品、方案和 marketplace 类型：

- Azure 资源管理器
    - Azure 应用程序
    - SaaS
    - 虚拟机
- 托管的测试驱动器
    - 当前不支持 Business Central (Dynamics 365) 
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 逻辑应用在支持模式下 (仅) 
- Power BI

有关配置其中一个测试驱动器的详细信息，请参阅 [测试驱动器技术配置](./test-drive-technical-configuration.md)。 

### <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器

此部署模板包含构成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 Azure 资源管理器测试驱动器适用于这些产品/服务类型： 

- Azure 应用程序
- SaaS
- 虚拟机

>[!NOTE]
>这是虚拟机和 Azure 应用程序产品/服务的唯一测试驱动器选项。

### <a name="hosted-test-drive-recommended"></a> (推荐的托管测试驱动器) 

托管的测试驱动器通过让 Microsoft 主机和维护执行测试驱动器用户预配和取消预配的服务，消除了安装的复杂性。 如果有 Microsoft AppSource 上的产品/服务，请构建你的测试驱动器以连接到 Dynamics AX/CRM 实例。 你可以使用以下 AppSource 提供类型：

- 使用 [Dynamics 365 为](partner-center-portal/create-new-customer-engagement-offer.md) 客户参与系统（如销售、服务、项目服务和现场服务）提供客户参与。
- 使用 [Dynamics 365 进行](partner-center-portal/create-new-operations-offer.md) 财务和运营企业资源规划系统（如财务、运营和制造、供应链）的操作。

### <a name="logic-app-test-drive"></a>逻辑应用测试驱动器

这种类型的测试驱动器不由 Microsoft 托管，使用 Azure 资源管理器 (ARM) 用于 Dynamics AX/CRM 产品/服务的模板。 需要运行 ARM 模板，才能在 Azure 订阅中创建所需的资源。 逻辑应用测试驱动器当前仅提供支持模式，Microsoft 不建议使用此驱动器，有关配置逻辑应用测试驱动器的详细信息，请参阅 [测试驱动器技术配置](./test-drive-technical-configuration.md)。

### <a name="power-bi-test-drive"></a>Power BI 体验版

这只是一个指向自定义生成的仪表板的嵌入链接。 仅演示交互 Power BI 视觉对象的任何产品都应使用这种类型的测试驱动器。

## <a name="transforming-examples"></a>转换示例

将资源体系结构转换为测试驱动器的过程可能会很困难。 请查看这些有关如何最好地转换当前体系结构的示例。

[将网站模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[将虚拟机模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[将现有资源管理器模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>从测试驱动器生成潜在顾客

商业市场测试驱动器是营销人员的极佳工具。 建议在启动时将其合并到市场推广工作中，以便为业务生成更多的潜在顾客。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)。

如果你结束了对测试驱动器的潜在客户的交易，请确保在 [Microsoft 合作伙伴销售连接](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)上注册它。 此外，我们还希望知道你的客户入选，其中的测试驱动器扮演了某个角色。

## <a name="other-resources"></a>其他资源

其他体验版资源：

- [试用版驱动最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）

## <a name="next-step"></a>后续步骤

- [体验版技术配置](test-drive-technical-configuration.md)