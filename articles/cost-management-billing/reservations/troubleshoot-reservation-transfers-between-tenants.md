---
title: 更改 Azure 预留目录
description: 本文帮助预留项所有者将预留订单从一个 Azure Active Directory 租户（目录）转移到另一个租户。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.openlocfilehash: 183071454cbe6c9185ecb1868abfe1ac217e9519
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108143658"
---
# <a name="change-an-azure-reservation-directory-between-tenants"></a>更改租户之间的 Azure 预留目录

本文帮助预留所有者将预留订单的目录从一个 Azure Active Directory 租户（目录）更改到另一个租户。 当你更改某个预留订单的目录时，会删除对该预留订单以及从属预留项的任何 Azure RBAC 访问权限。 在更改后只有你才拥有访问权限。 更改目录不会更改预留订单的计费所有权。 将更改父预留订单和从属预留项的目录。

更改预留订单的目录不需要预留交换和取消。

将某个预留项的目录更改到另一租户后，你可能还想要将其他所有者添加到该预留项。 有关详细信息，请参阅[默认情况下谁可以管理预留项](view-reservations.md#who-can-manage-a-reservation-by-default)。

更改某个预留订单的目录时，该订单下的所有预留项也会随之一起转移。

## <a name="change-a-reservation-orders-directory"></a>更改预留订单的目录

使用以下步骤将预留订单的目录及其从属预留项更改到另一租户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你不是计费管理员，但你是预留项所有者，请导航到“预留”并转到步骤 5。
1. 导航到“成本管理 + 计费”。
    - 如果你是 EA 管理员，请在左侧菜单中选择“计费范围”，然后在计费范围列表中选择一个范围。
    - 如果你是 Microsoft 客户协议计费配置文件所有者，请在左侧菜单中选择“计费配置文件”。 在计费配置文件列表中选择一个计费配置文件。
1. 此时将显示 EA 注册或计费配置文件的完整预留项列表。
1. 选择要转移的预留项。
1. 在预留项详细信息中选择“预留订单 ID”。
1. 在预留订单中选择“更改目录”。
1. 在“更改目录”窗格中，选择要将预留项转移到的 Azure AD 目录，然后选择“确认”。

## <a name="next-steps"></a>后续步骤

- 有关预留的详细信息，请参阅[什么是 Azure 保留？](save-compute-costs-reservations.md)。