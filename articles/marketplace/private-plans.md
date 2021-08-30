---
title: Microsoft 商业市场中的专用计划
description: Microsoft 商业市场中面向应用和服务发布者的专用计划（Azure 市场）。
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.date: 08/13/2021
ms.openlocfilehash: 1048aa89e13f22e6708a3cb86eaa1cb4332bc2a1
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326331"
---
# <a name="private-plans-in-the-microsoft-commercial-marketplace"></a>Microsoft 商业市场中的专用计划

发布者使用专用计划，因此专用计划仅显示给目标客户。 本文介绍了专用计划的选项和优势。

## <a name="unlock-enterprise-deals-with-private-plans"></a>通过专用计划促进企业达成交易

专用计划允许发布者为目标客户提供具有企业所需功能的专用定制解决方案：

- 协商定价：让发布者进一步放开公开销售套餐的折扣和还价。
- 专属条款和条件：让发布者针对特定的客户定制条款和条件。
- 专业配置：让发布者根据单个客户的需求定制其虚拟机、Azure 应用程序和软件即服务 (SaaS)。 在向所有客户推出新产品之前，此选项还可让发布者提供产品功能的预览访问。

通过专用计划，发布者能够利用公开市场的规模和全球可用性，使用所需的控制机制灵活协商和提供自定义的交易方式与配置。 现在，企业可根据自己的预期采购和销售解决方案。

## <a name="create-private-plans"></a>创建专用计划

对于包含计划的新的或现有产品/服务，发布者可以通过创建新计划（以前称为 SKU）轻松创建新的专用变体，并将其标记为专用。 每个产品/服务最多可以有 45 个专用计划。

<!--- [Private SKUs]() --->

专用计划适用于以下产品/服务类型：

- Azure 虚拟机
- Azure 应用程序（作为解决方案模板或托管应用程序实现）
- 托管服务
- SaaS 产品/服务

专用计划是产品/服务的组成部分，只能由目标客户看到和购买。 专用计划仅可供目标客户查看和购买。 在 Azure 全球云和 Azure 政府云中，客户可以使用专用计划。

专用计划可以重复使用已为公共计划发布的基础映像和/或产品（服务）元数据。 此选项可让发布者创建公共产品/服务的多个专用变体，而无需发布相同基本映像和产品/服务元数据的多个版本。 （仅适用于 Azure 虚拟机和 Azure 应用程序产品/服务）当某个专用计划与公共计划共享基本映像时，对产品/服务基本映像所做的任何更改将使用该基本映像在所有公共和专用计划之间传播。

对于仅包含专用计划的新产品/服务，发布者可与往常一样创建产品/服务，然后将计划标记为专用。 对于仅包含专用计划的产品/服务，不与该产品/服务关联的客户无法在 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)中发现或访问它。

>[!NOTE]
>仅包含专用计划的产品/服务不会显示在公共 Azure 市场或 AppSource 中。

## <a name="target-customers-with-private-plans"></a>确定专用计划的目标客户

对于新的和现有的专用计划，发布者都可以使用订阅标识符来确定目标客户。 对于 Azure 虚拟机、Azure 应用程序和托管服务产品/服务，发布者可将专用计划的可用性限制为单个 Azure 订阅 ID，或上传最多包含 10,000 个 Azure 订阅 ID 的 CSV。 对于 SaaS 产品/服务，发布者可以关联 Azure Active Directory 租户 ID，以通过手动方法或 CSV 上传方法来限制专用计划的可用性。

认证并发布产品/服务后，可以使用“同步专用订阅”功能，在计划中更新或删除客户。 此功能可让发布者快速轻松地更新可以看到专用计划的客户列表，且无需再次认证或发布产品/服务。

## <a name="deploying-a-private-plan"></a>部署专用计划

登录到 Azure 门户后，客户可按照以下步骤选择你的专用计划。

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
1. 在“Azure 服务”下，选择“创建资源” 。
1. 在“新建”页上的“Azure 市场”旁边，选择“全部查看”  。 此时会显示“市场”页。
1. 在左侧导航栏中，选择“专用计划”。

> [!NOTE]
> 只能在 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)中发现专用计划。 它们不会显示在 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure 市场](https://azuremarketplace.microsoft.com)中。 若要详细了解如何发布到不同的商业市场在线商店，请参阅[列表选项简介](./determine-your-listing-type.md)。

专用计划也会显示在搜索结果中，并与任何其他产品/服务一样，可以通过命令行和 Azure 资源管理器模板进行部署。

[![[搜索结果中显示的专用产品/服务。]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

>[!Note]
>通过云解决方案提供商 (CSP) 计划的分销商计划建立的订阅不支持私有计划。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
