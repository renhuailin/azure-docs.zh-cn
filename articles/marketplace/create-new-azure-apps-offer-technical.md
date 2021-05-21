---
title: 如何为 Azure 应用产品/服务添加技术详细信息
description: 了解如何为合作伙伴中心的 Azure 应用产品/服务添加技术详细信息。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94369963"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>如何为 Azure 应用产品/服务添加技术详细信息

本文介绍如何输入技术详细信息，以帮助 Microsoft 商业市场连接到你的解决方案。 使用此连接，可以为选择获取你的产品/服务的客户预配产品/服务并进行管理。

仅当产品/服务包含托管的应用程序时才可完成此部分，该托管的应用程序将使用[市场计量的计费 API](partner-center-portal/marketplace-metering-service-apis.md) 发出计量事件，并且具有要通过 Azure AD 安全令牌进行身份验证的服务。 有关详细信息，请参阅有关不同身份验证选项的[市场计量服务身份验证策略](partner-center-portal/marketplace-metering-service-authentication.md)。

## <a name="technical-configuration-offer-level"></a>技术配置（产品/服务级别）

“技术配置”选项卡仅适用于你创建使用[市场计量计费 API](partner-center-portal/marketplace-metering-service-apis.md) 发出计费事件的托管应用程序的情况。 如果是这样，请完成以下步骤。 否则，请转到[后续步骤](#next-steps)。 

有关这些字段的详细信息，请参阅[为商业市场计划 Azure 应用程序产品/服务](plan-azure-application-offer.md#technical-configuration)。

1. 在“技术配置”选项卡上，提供“Azure Active Directory 租户 ID”和“Azure Active Directory 应用程序 ID”，用于验证我们的两个服务之间的连接是否位于经过身份验证的通信后面。

1. 选择“保存草稿”，然后转到下一选项卡：计划概述。

## <a name="next-steps"></a>后续步骤

- [如何为 Azure 应用程序产品/服务创建计划](create-new-azure-apps-offer-plans.md)
