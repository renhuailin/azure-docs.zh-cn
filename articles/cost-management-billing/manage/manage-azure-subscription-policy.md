---
title: 管理 Azure 订阅策略
description: 了解如何管理 Azure 订阅策略，以便在将 Azure 订阅移入和移出目录时进行控制。
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631725"
---
# <a name="manage-azure-subscription-policies"></a>管理 Azure 订阅策略

>[!NOTE]
>此功能目前以预览版提供，将会逐步推出，因此并非所有人都可在 Azure 门户中看到这种体验。

本文可帮助你为订阅操作配置 Azure 订阅策略，以便在将 Azure 订阅移入和移出目录时进行控制。

## <a name="prerequisites"></a>先决条件

- 只有目录[全局管理员](../../active-directory/roles/permissions-reference.md#global-administrator)可以编辑订阅策略。 在编辑订阅策略之前，全局管理员必须先[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。 然后才能编辑订阅策略。
- 其他所有用户只能读取当前的策略设置。

## <a name="available-subscription-policy-settings"></a>可用的订阅策略设置

将 Azure 订阅移入和移出目录时，请使用以下策略设置来进行控制。

### <a name="subscriptions-leaving-aad-directory"></a>将订阅移出 AAD 目录

此策略允许或阻止用户将订阅移出当前目录。 [订阅所有者](../../role-based-access-control/built-in-roles.md#owner)可[将 Azure 订阅的目录更改](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)为他们在其中拥有成员身份的其他目录。 这会带来治理挑战，因此全局管理员可允许或禁止目录用户更改目录。

### <a name="subscriptions-entering-aad-directory"></a>将订阅移入 AAD 目录

此策略允许或阻止对当前目录具有访问权限的其他目录中的用户将订阅移入当前目录。 [订阅所有者](../../role-based-access-control/built-in-roles.md#owner)可[将 Azure 订阅的目录更改](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)为他们在其中拥有成员身份的其他目录。 这会带来治理挑战，因此全局管理员可允许或禁止目录用户更改目录。

### <a name="exempted-users"></a>豁免用户

出于治理原因，全局管理员可阻止将所有订阅目录移入到当前目录中。 不过，他们可能想要允许特定用户执行移入或移出操作。 无论是哪种情况，都可配置豁免用户列表，使这些用户能够绕过应用于其他所有用户的策略设置。

## <a name="setting-subscription-policy"></a>设置订阅策略

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 导航到“订阅”。 “管理策略”显示在命令栏上。  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="屏幕截图显示了订阅中的“管理策略”。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. 选择“管理策略”，查看当前为目录设置的订阅策略的详细信息。 具有[提升权限](../../role-based-access-control/elevate-access-global-admin.md)的全局管理员可以编辑设置，包括添加或删除豁免用户。  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="屏幕截图显示了特定的策略设置和豁免用户。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. 选择底部的“保存更改”，以保存更改。 更改将立即生效。

## <a name="read-subscription-policy"></a>读取订阅策略

非全局管理员仍可导航到“订阅策略”区域来查看目录的策略设置。 他们不能进行任何编辑。 出于隐私原因，他们无法查看豁免用户的列表。 只要目录设置允许，他们就可查看其全局管理员，以提交策略更改请求。

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="屏幕截图显示了使用读者身份时订阅中的管理策略。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>后续步骤

- 阅读[“成本管理 + 计费”文档](../index.yml)