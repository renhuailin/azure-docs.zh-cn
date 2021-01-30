---
title: Microsoft 商业应用商店中的专用产品/服务
description: 适用于应用和服务发布者的 Microsoft 商用 marketplace 中的私有产品/服务。
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 01/28/2021
ms.openlocfilehash: 786ecbf553ace6a90515347e8138eeb6e022589b
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063090"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 商业应用商店中的专用产品/服务

专用产品/服务也称为专用计划，使发布者能够创建仅对目标客户可见的计划。 本文介绍专用产品/服务的选项和优点。

## <a name="unlock-enterprise-deals-with-private-offers"></a>通过专用产品/服务解锁企业交易

通过创建专用产品/服务，发布者可以为具有企业所需功能的目标客户提供自定义解决方案：

- 议价：让发布者进一步开放公开销售套餐的折扣和还价。
- 专属条款和条件：让发布者针对特定的客户定制条款和条件。
- 通过 _专用配置_，发布者可以将其虚拟机、Azure 应用程序和软件即服务作为服务 (SaaS) 到单个客户的需求。 在将新产品功能启动到所有客户之前，此选项还允许发布者提供对新产品功能的预览访问权限。

专用产品/服务允许发布者利用公有 marketplace 的规模和全球可用性，并提供所需的灵活性和控制度来协商和交付自定义交易和配置。 企业现在可以按预期方式购买和销售 

## <a name="create-private-offers-using-plans"></a>使用计划创建专用产品/服务

对于 *具有计划的新的或现有的产品/服务*，发布者可以通过创建新计划 (以前称为 sku) 并将它们标记为专用，来轻松创建新的专用变体。 每个产品/服务最多可以有45个私有计划。

<!--- [Private SKUs]() --->

私有计划适用于以下产品/服务类型：

- Azure 虚拟机
- 作为解决方案模板或托管应用程序实现 Azure 应用程序 () 
- 托管服务
- SaaS 产品/服务

私人计划是产品/服务的组成部分，只由目标客户可购买。 专用计划仅供目标客户查看和可购买。  在 Azure Global 和 Azure 政府版中，客户可以使用私有计划。

私有计划可以重复使用基本映像和/或提供已为公用计划发布的元数据。 此选项可让发布者创建公共产品/服务的多个专用变体，而无需发布同一基础映像的多个版本并提供元数据。 仅对于 Azure 虚拟机和 Azure 应用程序提供，当专用计划与公共计划共享基本映像时，对产品/服务的基本映像所做的任何更改都将使用该基本映像传播到所有公用和专用计划。

对于 *仅包含私有计划的新产品*/服务，发布者可以创建其产品/服务作为任何其他产品/服务，然后将计划标记为私有。 只有不与产品/服务关联的客户才能在 [Azure 门户](https://azure.microsoft.com/features/azure-portal/) 中发现或访问仅具有私有计划的产品/服务。

>[!NOTE]
>仅包含私有计划的产品/服务将不会显示在公共 Azure Marketplace 或 AppSource 中。

## <a name="target-customers-with-private-offers"></a>提供专用产品/服务的目标客户

对于新的和现有的专用套餐，发布者可以使用订阅标识符来确定目标客户。 对于 Azure 虚拟机、Azure 应用程序和托管服务产品/服务，发布者可以将私有计划的可用性限制为单个 Azure 订阅 ID，或上传最多10000个 Azure 订阅 Id 的 CSV。 对于 SaaS 产品/服务，发布者可以使用手动或 CSV 上传方法关联 Azure Active Directory 租户 ID，以限制私有计划的可用性。

一旦产品/服务已通过认证和发布，就可以使用 "同步专用订阅" 功能在计划中更新或删除客户。 利用此功能，发布者可以快速轻松地更新显示了私有计划的客户列表，而无需证明或再次发布该产品/服务。

## <a name="deploying-private-offers"></a>部署专用产品/服务

登录到 Azure 门户后，客户可以按照以下步骤选择你的专用产品/服务。

1. 登录到 [Azure 门户](https://ms.portal.azure.com/)。
1. 在 " **Azure 服务**" 下，选择 " **创建资源**"。
1. 在 " **新建** " 页上的 " **Azure Marketplace**" 旁边，选择 " **查看全部**"。 此时将显示 "Marketplace" 页。
1. 在左侧导航栏中，选择 " **专用产品/服务**"。

> [!NOTE]
> 只能在 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)中发现专用产品/服务。 它们不显示在 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure Marketplace](https://azuremarketplace.microsoft.com)中。 若要了解有关发布到不同的商业 marketplace 在线商店的详细信息，请参阅 [列表选项简介](./determine-your-listing-type.md)。

专用产品/服务还将显示在搜索结果中，并可通过命令行和 Azure 资源管理器模板进行部署，就像任何其他产品。

![[专用套餐]](./media/marketplace-publishers-guide/private-offer.png)

专用套餐也会显示在搜索结果中。 只需查找 **专用** 徽章即可。

>[!Note]
>通过云解决方案提供商的经销商 (CSP) program 建立的订阅不支持专用产品/服务。


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
