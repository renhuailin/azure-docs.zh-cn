---
title: 如何为 Azure 应用程序产品/服务添加技术详细信息
description: 了解如何在合作伙伴中心添加 Azure 应用程序产品/服务的技术详细信息。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369963"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>如何为 Azure 应用程序产品/服务添加技术详细信息

本文介绍如何输入有助于 Microsoft 商用 marketplace 连接到解决方案的技术详细信息。 此连接使我们能够为客户预配你的产品/服务，前提是他们选择获取并管理你的产品。

仅当你的产品/服务包括一个托管应用程序，该应用程序将使用 [Marketplace 计费 api](partner-center-portal/marketplace-metering-service-apis.md) 发出计数事件，并具有将使用 Azure AD 安全令牌进行身份验证的服务时，才能完成本部分。 有关详细信息，请参阅不同身份验证选项的 [Marketplace 计量服务身份验证策略](partner-center-portal/marketplace-metering-service-authentication.md) 。

## <a name="technical-configuration-offer-level"></a>技术配置 (服务级别) 

仅当你将创建一个托管应用程序，该应用程序使用 [Marketplace 计费 api](partner-center-portal/marketplace-metering-service-apis.md)发出计数事件时，" **技术配置** " 选项卡才适用。 如果是这样，请完成以下步骤。 否则，请继续执行 [后续步骤](#next-steps)。 

有关这些字段的详细信息，请参阅 [为商用 Marketplace 计划 Azure 应用程序产品](plan-azure-application-offer.md#technical-configuration)。

1. 在 " **技术配置** " 选项卡上，提供 **Azure Active Directory 租户 id** 和 **Azure Active Directory 应用程序 id** ，用于验证两个服务之间的连接是否在经过身份验证的通信之后。

1. 选择 " **保存草稿** "，然后继续下一步选项卡：计划概述。

## <a name="next-steps"></a>后续步骤

- [如何为 Azure 应用程序产品/服务创建计划](create-new-azure-apps-offer-plans.md)
