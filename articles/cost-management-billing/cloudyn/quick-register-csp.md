---
title: 在 Azure 中使用 CSP 合作伙伴信息注册 Cloudyn
description: 详细了解合作伙伴将其客户加入到 Cloudyn 门户的注册过程。
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 95d8a425314ad1c968915085b35ca89aa3f62711
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543315"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>注册 CSP 合作伙伴计划并查看成本数据

作为 CSP 合作伙伴和已注册的 Cloudyn 用户，你可以在 Cloudyn 中查看和分析云支出。 [Azure 成本管理原生可用于已将其客户加入 Microsoft 客户协议并已购买 Azure 计划的直接合作伙伴](../costs/get-started-partners.md)。

注册后可以访问 Cloudyn 门户。 本快速入门将详细介绍创建 Cloudyn 试用订阅和登录到 Cloudyn 门户所需的注册过程。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="configure-indirect-csp-access-in-cloudyn"></a>在 Cloudyn 中配置间接 CSP 访问权限

默认情况下，仅直接 CSP 可以访问合作伙伴中心 API。 但是，直接 CSP 提供商可以使用 Cloudyn 中的实体组为他们的间接 CSP 客户或合作伙伴配置访问权限。

若要为间接 CSP 客户或合作伙伴启用访问权限，请完成以下步骤，以使用 Cloudyn 实体组将间接 CSP 数据分段。 然后，向实体组分配适当的用户权限。

1. 使用[创建实体](tutorial-user-access.md#create-and-manage-entities)上的信息创建实体组。
2. 按照[将订阅分配到费用实体](https://www.youtube.com/watch?v=d9uTWSdoQYo)上的步骤操作。 将间接 CSP 客户的帐户及其 Azure 订阅与之前创建的实体关联。
3. 按照[创建具有管理员权限的用户](tutorial-user-access.md#create-a-user-with-admin-access)上的步骤创建具有管理员权限的用户帐户。 然后，确保该用户帐户拥有之前为间接帐户创建的特定实体的管理员权限。

间接 CSP 合作伙伴使用为他们创建的帐户登录到 Cloudyn 门户。


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 CSP 信息注册了 Cloudyn。 此外，还会登录到 Cloudyn 门户并开始查看成本数据。 若要了解有关 Cloudyn 的详细信息，请继续学习 Cloudyn 的教程。

> [!div class="nextstepaction"]
> [查看使用情况和成本](tutorial-review-usage.md)