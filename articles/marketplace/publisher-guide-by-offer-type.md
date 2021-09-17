---
title: 发布指南（按产品/服务类型）- Microsoft 商业市场
description: 本文介绍 Microsoft 商业市场（Azure 市场）中提供的产品/服务类型。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/20/2021
ms.openlocfilehash: b5b969a5e390b59e60fb0dee47e2335541532730
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633766"
---
# <a name="publishing-guide-by-offer-type"></a>发布指南（按产品/服务类型）

本文介绍商业市场中提供的产品/服务类型。 “产品/服务类型”定义产品/服务结构，其中包括元数据、项目和商业市场中呈现的其他内容。

[确定发布选项](determine-your-listing-type.md)后，必须先选择产品/服务类型，然后才能开始在合作伙伴中心创建产品/服务。 套餐类型对应于你要发布的解决方案、应用或服务套餐的类型，并取决于与 Microsoft 产品和服务的匹配情况。

> [!NOTE]
> 选择产品/服务类型后，无法将产品/服务更改为其他类型。 要创建不同的产品/服务类型，需要创建新产品/服务。

可通过不同方式配置单个产品/服务类型，以启用不同的发布选项、列表选项、预配或定价。 套餐类型的发布选项和配置也取决于套餐资格和技术要求。

在创建产品/服务之前，请务必查看店面和产品/服务类型资格要求以及技术方面的发布要求。

## <a name="list-of-offer-types"></a>产品/服务类型列表

下表显示了合作伙伴中心的商业市场产品/服务类型。

| **产品/服务类型**    | **说明**  |
| :------------------- | :-------------------|
| [**Azure 应用程序**](plan-azure-application-offer.md) | 有两种类型的 Azure 应用程序计划：“解决方案模板”和“托管应用程序”。 这两种计划类型都支持超越单个虚拟机 (VM) 自动部署和配置解决方案。 可以自动执行提供多个资源（包括 VM、网络和存储资源）的过程，以便提供 IaaS 解决方案等的复杂解决方案。 这两种计划类型都可以采用多种不同类型的 Azure 资源，包括但不限于 VM。<ul><li>“解决方案模板”计划是在商业市场中发布解决方案的主要方式之一。 解决方案模板计划在商业市场中不可交易，但可用于部署通过商业市场计费的付费 VM 产品/服务。 当客户将管理解决方案，并且通过另一个计划对事务进行计费时，使用解决方案模板计划类型。</li><br><li>使用“托管应用程序”计划可轻松生成完全托管的统包应用程序并交付给客户。 它们具有与解决方案模板计划相同的功能，但有一些主要区别：</li><ul><li> 资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。</li><li>作为发布者，你可以指定当前支持的解决方案的成本，并通过商业市场支持事务。</li></ul>当你或你的客户要求解决方案由合作伙伴管理或者你将部署基于订阅的解决方案时，请使用托管应用程序计划类型。</ul> |
| [**Azure 容器**](marketplace-containers.md) | 如果解决方案是预配为基于 Kubernetes 的 Azure 容器服务的 Docker 容器映像，请使用“Azure 容器”产品/服务类型。 |
| [**Azure 虚拟机**](marketplace-virtual-machines.md) | 将虚拟设备部署到与客户关联的订阅时，请使用“虚拟机”产品/服务类型。 |
| [**咨询服务**](./plan-consulting-service-offer.md) | 咨询服务有助于将客户与服务联系起来，以支持和扩展他们对 Azure、Dynamics 365 或 Power Suite 服务的使用。|
| [**Dynamics 365**](marketplace-dynamics-365.md) | 发布依赖或扩展 Dynamics 365 Business Central、Dynamics 365 Customer Engagement、Power Apps 和 Finance and Operations 应用的 AppSource 产品/服务。|
| [**IoT Edge 模块**](marketplace-iot-edge.md) | Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Microsoft 服务（如 Azure 流分析）、第三方服务，或你自己特定于解决方案的代码。 |
| [**托管服务**](./plan-managed-service-offer.md) | 通过 [Azure Lighthouse](../lighthouse/overview.md) 创建托管服务产品/服务和管理客户委托的订阅或资源组。|
| [**Power BI 应用**<br/>**Microsoft 365**](marketplace-dynamics-365.md) | 发布依赖或扩展 Power BI 和 Microsoft 365 的 AppSource 产品/服务。|
| [**服务型软件**](plan-saas-offer.md) | 使用服务型软件 (SaaS) 产品/服务类型可让客户以订阅的形式购买基于 SaaS 的技术解决方案。 有关 SaaS 产品/服务的单一登录要求的信息，请参阅[商业市场中的 Azure AD 和可交易的 SaaS 产品/服务](azure-ad-saas.md)。 |

> [!IMPORTANT]
> **SaaS 产品/服务和 Microsoft 365 加载项**：若要了解交易功能如何影响市场客户查看和购买产品/服务的方式的特定详细信息，请参阅[在商业市场中进行交易](marketplace-commercial-transaction-capabilities-and-considerations.md)。 对于 SaaS 产品/服务，产品/服务的交易功能和类别选择将决定产品/服务将在哪个在线商店发布。

## <a name="next-steps"></a>后续步骤

- 查看相应文章中产品/服务类型的资格要求，以便最终确定产品/服务的选择和配置。
- 查看每个在线商店的发布模式，例如，解决方案如何映射到产品/服务类型和配置。
