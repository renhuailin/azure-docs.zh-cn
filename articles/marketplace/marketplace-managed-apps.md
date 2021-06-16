---
title: Azure 托管应用程序产品/服务发布指南 - Azure 市场
description: 本文介绍在 Azure 市场中发布托管应用程序的要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 73c8daf49ccdfeee1903a3c0ad823cdd3e52c141
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537854"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 托管应用程序发布指南

Azure 托管应用程序产品/服务是在 Azure 市场中发布 Azure 应用程序的一种方法。 托管应用程序是通过 Azure 市场部署并计费的交易产品/服务。 用户看到的商品选项是“立即获取”。

本文介绍了托管应用程序产品/服务类型的要求。

请在以下条件下使用托管应用程序产品/服务类型：

- 通过使用虚拟机 (VM) 或基于基础结构即服务 (IaaS) 的完整解决方案，为客户部署基于订阅的解决方案。
- 你或你的客户需要由合作伙伴来管理该解决方案。

>[!NOTE]
>例如，合作伙伴可以是系统集成商或托管服务提供商 (MSP)。  

## <a name="managed-application-offer-requirements"></a>托管应用程序产品/服务要求

|要求 |详细信息  |
|---------|---------|
|Azure 订阅 | 托管应用程序必须部署到客户的订阅，但可由第三方管理。 |
|计费和计量    |  在客户的 Azure 订阅中提供资源。 使用即用即付付款模型的 Azure 资源通过 Microsoft 与客户交易，并通过客户的 Azure 订阅计费。 <br><br> 对于自带许可的 Azure 资源，Microsoft 会对客户订阅中产生的任何基础结构成本计费，但由你直接与客户处理软件许可费用事宜。        |
|Azure 托管应用程序包    |   已配置的“Azure 资源管理器模板”和“创建 UI 定义”，会用于将应用程序部署到客户的订阅。<br><br>若要详细了解如何创建托管应用程序，请参阅[托管应用程序概述](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)。|

---

> [!NOTE]
> 托管应用程序必须可以通过 Azure 市场来部署。 如果担心客户沟通方面的问题，请在启用潜在客户分享后与感兴趣的客户联系。  

> [!Note]
> 现已可选择加入云解决方案提供商 (CSP) 合作伙伴渠道。 若要详细了解如何通过 Microsoft CSP 合作伙伴渠道来推广产品/服务，请参阅[云解决方案提供商](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[使用 Azure 市场发展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 有关详细信息，请参阅[创建 Azure 应用程序产品/服务](azure-app-offer-setup.md)。
