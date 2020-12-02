---
title: 按产品/服务类型发布指南-Microsoft 商用 marketplace
description: 本文介绍了 Microsoft 商业应用商店中提供的产品/服务类型。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 2191a4df5b319ec16a4a6116aa99cfac50c87d9b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462912"
---
# <a name="publishing-guide-by-offer-type"></a>发布指南（按产品/服务类型）

本文介绍了商业应用商店中提供的产品/服务类型。 *产品/服务类型* 定义产品/服务结构，其中包括商业应用商店中提供的元数据、项目和其他内容。

[确定发布选项](determine-your-listing-type.md)后，必须先选择产品/服务类型，然后才能开始在合作伙伴中心创建产品/服务。 套餐类型对应于你要发布的解决方案、应用或服务套餐的类型，并取决于与 Microsoft 产品和服务的匹配情况。

你可以通过不同方式配置单个产品/服务类型，以启用不同的发布选项、列表选项、设置或定价。 套餐类型的发布选项和配置也取决于套餐资格和技术要求。

在创建产品/服务之前，请务必查看在线商店并提供类型资格要求和技术发布要求。

## <a name="list-of-offer-types"></a>产品/服务类型列表

下表显示了合作伙伴中心的商业 marketplace 产品/服务类型。

| **产品/服务类型**    | **说明**  |
| :------------------- | :-------------------|
| [**Azure 应用程序**](plan-azure-application-offer.md) | 有两种类型的 Azure 应用程序计划： _解决方案模板_ 和 _托管应用程序_。 这两种计划类型支持自动部署和配置解决方案，而不是 (VM) 的单个虚拟机。 可以自动执行提供多个资源（包括 Vm、网络和存储资源）的过程，以便提供诸如 IaaS 解决方案的复杂解决方案。 这两种计划类型可以使用多种不同类型的 Azure 资源，包括但不限于 Vm。<ul><li>**解决方案模板** 计划是在商业市场中发布解决方案的主要方法之一。 解决方案模板计划并不事务在商业应用商店中，但可用于部署通过商业市场计费的付费 VM 产品/服务。 当客户将管理解决方案，并且通过另一个计划对交易进行计费时，使用解决方案模板计划类型。</li><br><li>使用 **托管应用程序** 计划，可以为客户轻松构建和交付完全托管的全包式应用程序。 它们与解决方案模板计划具有相同的功能，但有一些主要区别：</li><ul><li> 资源将部署到资源组，并由应用程序的发布者进行管理。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。</li><li>作为发布者，你可以指定当前支持的解决方案的成本，并通过商业应用商店支持事务。</li></ul>当你或你的客户要求解决方案由合作伙伴管理或者你将部署基于订阅的解决方案时，请使用托管应用程序计划类型。</ul> |
| [**Azure 容器**](marketplace-containers.md) | 如果解决方案是预配为基于 Kubernetes 的 Azure 容器服务的 Docker 容器映像，请使用 Azure 容器产品类型。 |
| [**Azure 虚拟机**](marketplace-virtual-machines.md) | 将虚拟设备部署到与客户关联的订阅时，请使用“虚拟机”产品/服务类型。 |
| [**咨询服务**](./plan-consulting-service-offer.md) | 咨询服务有助于将客户连接到服务，以支持和扩展其对 Azure、Dynamics 365 或 Power Suite 服务的使用。|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | 可以发布生成或扩展 Dynamics 365 Business Central、Dynamics 365 客户参与、电源应用和财务和操作应用的 AppSource 产品/服务。|
| [**IoT Edge 模块**](iot-edge-module.md) | Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Microsoft 服务（如 Azure 流分析）、第三方服务，或你自己特定于解决方案的代码。 |
| [**托管服务**](partner-center-portal/create-new-managed-service-offer.md) | 可以通过 [Azure Lighthouse](../lighthouse/overview.md)创建托管服务提供和管理客户委托的订阅或资源组。|
| [**Power BI 应用** <br/>**Microsoft 365**](appsource-offer-publishing-guide.md) | 您可以发布 Power BI 和 Microsoft 365 上生成或扩展的 AppSource 产品/服务。|
| [**软件即服务**](plan-saas-offer.md) | 使用 "软件即服务" (SaaS) 产品/服务类型，使客户能够购买基于 SaaS 的技术解决方案作为订阅。 有关 SaaS 产品/服务的单一登录要求的信息，请参阅 [商业应用商店中的 Azure AD 和事务 SaaS 产品](azure-ad-saas.md)。 |


## <a name="next-steps"></a>后续步骤

- 在产品/服务类型的相应文章中查看符合性要求，以完成产品/服务的选择和配置。
- 查看每个在线商店的发布模式，获取有关解决方案如何映射到产品/服务类型和配置的示例。