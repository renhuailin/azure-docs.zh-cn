---
title: 什么是 Azure Lighthouse？
description: 借助 Azure Lighthouse，服务提供商可为其客户提供自动化程度更高且更高效的规模级托管服务。
ms.date: 07/13/2021
ms.topic: overview
ms.openlocfilehash: 2da14a65f146a181af39a8c06a8089fee9010467
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666557"
---
# <a name="what-is-azure-lighthouse"></a>什么是 Azure Lighthouse？

Azure Lighthouse 允许多租户管理，具有跨资源的可伸缩性、更高的自动化程度以及增强的管理。

借助 Azure Lighthouse，服务提供商可以使用 [Azure 平台内置的全面而强大的工具](concepts/architecture.md)来提供托管服务。 客户对有权访问其租户的人员、可以访问的资源以及可执行的操作保持控制。 此产品/服务还可让[企业 IT 组织](concepts/enterprise.md)跨多个租户管理资源。

[跨租户管理体验](concepts/cross-tenant-management-experience.md)可让你使用 [Azure Policy](how-to/policy-at-scale.md)、[Azure Sentinel](how-to/manage-sentinel-workspaces.md)、[Azure Arc](how-to/manage-hybrid-infrastructure-arc.md) 等 Azure 服务更有效率地工作。 用户可以[在活动日志中](how-to/view-service-provider-activity.md)查看所做更改以及更改人，该日志存储在客户的租户中（并且可供管理租户中的用户查看）。

![Azure Lighthouse 概述示意图](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>优点

Azure Lighthouse 可帮助服务提供商有效地生成和交付托管服务。 优点包括：

- **大规模管理**：用于管理客户资源的客户参与和生命周期操作更加简单且更具可缩放性。 现有的 API、管理工具和工作流可以与委托的资源（包括在 Azure 外部托管的计算机）配合使用，而不管资源位于哪个区域。
- **为客户提供更好的可见性和控制**：客户可精确控制他们委托管理的范围以及允许的权限。 如果需要，他们可以[审核服务提供商操作](how-to/view-service-provider-activity.md)并完全删除访问权限。
- **全面统一的平台工具**：Azure Lighthouse 适用于现有工具和 API、[Azure 托管应用程序](concepts/managed-applications.md)和合作伙伴计划，如[云解决方案提供商计划 (CSP)](concepts/cloud-solution-provider.md)。 这种灵活性为重要的服务提供商方案提供支持，包括多种许可模型，如 EA、CSP 和即用即付。 可以将 Azure Lighthouse 集成到现有的工作流和应用程序中，并通过[关联合作伙伴 ID](how-to/partner-earned-credit.md) 来跟踪你对客户参与的影响。

## <a name="capabilities"></a>功能

Azure Lighthouse 提供了多种方法来协助简化参与和管理：

- **Azure 委派资源管理**：无需切换上下文和控制平面即可 [从自己的租户中安全地管理客户的 Azure 资源](concepts/architecture.md)。 可以将客户订阅和资源组委托给管理租户中的指定用户和角色，并可以根据需要删除访问权限。
- **新 Azure 门户体验**：在 Azure 门户中的 [“我的客户”页面](how-to/view-manage-customers.md)中查看跨租户信息。 使用相应的[“服务提供商”页面](how-to/view-manage-service-providers.md)，客户可以查看和管理其服务提供商访问。
- **Azure 资源管理器模板**：使用 ARM 模板 [加入委托的客户资源](how-to/onboard-customer.md)和 [执行跨租户管理任务](samples/index.md)。
- **Azure 市场中的托管服务产品**：通过专用或公开产品/服务 [为客户提供服务](concepts/managed-services-offers.md)，并将其自动加入 Azure Lighthouse。

> [!TIP]
> 类似的产品 [Microsoft 365 Lighthouse](/microsoft-365/lighthouse/m365-lighthouse-overview) 可帮助服务提供商大规模载入、监视和管理其 Microsoft 365 客户。 Microsoft 365 Lighthouse 当前为预览版。

## <a name="pricing-and-availability"></a>定价和可用性

使用 Azure Lighthouse 管理 Azure 资源不会产生额外的费用。 任何 Azure 客户或合作伙伴都可以使用 Azure Lighthouse。

## <a name="cross-region-and-cloud-considerations"></a>跨区域和云注意事项

Azure Lighthouse 是一项非区域性服务。 你可以管理位于不同[区域](../availability-zones/az-overview.md#regions)的委托资源。 但是，不支持跨[国家云](../active-directory/develop/authentication-national-cloud.md)和 Azure 公有云或跨两个不同的国家云进行订阅委托。

## <a name="support-for-azure-lighthouse"></a>Azure Lighthouse 的支持

如需关于使用 Azure Lighthouse 的帮助，请在 Azure 门户中[提出支持请求](..//azure-portal/supportability/how-to-create-azure-support-request.md)。 对于“问题类型”，请选择“技术”。 选择订阅，然后选择“Lighthouse”（在“监视与管理”下） 。

## <a name="next-steps"></a>后续步骤

- 了解[技术级别 Azure Lighthouse 的工作原理](concepts/architecture.md)。
- 了解[跨租户管理体验](concepts/cross-tenant-management-experience.md)。
- 了解如何[在企业内使用 Azure Lighthouse](concepts/enterprise.md)。
- 查看 Azure Lighthouse 的[可用性](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all)和 [FedRAMP 和 DoD CC SRG 审核范围](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)详细信息。
