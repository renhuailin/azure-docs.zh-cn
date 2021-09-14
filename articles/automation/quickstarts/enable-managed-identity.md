---
title: 快速入门 - 使用 Azure 门户为自动化帐户启用托管标识
description: 本快速入门可帮助你使用 Azure 门户为自动化帐户启用托管标识
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 14fc0af0b75639ae79c417645912a868a0df34f6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544506"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>快速入门：使用 Azure 门户为自动化帐户启用托管标识

本快速入门介绍如何为 Azure 自动化帐户启用托管标识。 有关托管标识如何与 Azure 自动化配合使用的详细信息，请参阅[托管标识](../automation-security-overview.md#managed-identities-preview)。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 一个 Azure 自动化帐户。 有关说明，请参阅[创建自动化帐户](create-account-portal.md)。

- 用户分配的托管标识。 有关说明，请参阅[创建用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。 用户分配的托管标识以及 Runbook 使用该标识管理的目标 Azure 资源必须在同一个 Azure 订阅中。

## <a name="enable-system-assigned-managed-identity"></a>启用系统分配的托管标识

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到自动化帐户。

1. 在“帐户设置”下，选择“标识(预览版)” ****  **** 。

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="导航到门户中的“标识”。":::

1. 将系统分配的“状态”选项设置为“开”，然后按“保存”  。 当系统提示确认时，请选择“是”。

   自动化帐户现在可以使用系统分配的标识，该标识注册到 Azure Active Directory (Azure AD)，由对象 ID 表示。

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="托管标识对象 ID。":::

## <a name="add-user-assigned-managed-identity"></a>添加用户分配的托管标识

本部分从上一部分结束的位置继续。

1. 选择“用户分配”选项卡，然后选择“+ 添加”或“添加用户分配的托管标识”，打开“添加用户分配的托管标识”页   。

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="门户中的“用户分配”选项卡。":::

1. 从“订阅”下拉列表中，为用户分配的托管标识选择订阅。

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="门户中的“添加用户分配”页面。":::

1. 在“用户分配的托管标识”下，选择现有的用户分配的托管标识，然后选择“添加” 。 随后你将返回到“用户分配”选项卡。

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="在门户中添加了用户分配的托管标识。":::


## <a name="clean-up-resources"></a>清理资源

如果不再需要附加到自动化帐户的用户分配的托管标识，请执行以下步骤：

1. 从“用户分配”选项卡中，选择用户分配的托管标识。

1. 从顶部菜单中选择“删除”，然后在提示确认时选择“是” 。

如果不再需要为自动化帐户启用系统分配的托管标识，请执行以下步骤：

1. 从“系统分配”选项卡的“状态”下，选择“关”  。

1. 从顶部菜单中选择“保存”，然后在提示确认时选择“是” 。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你为 Azure 自动化帐户启用了托管标识。 若要使用具有托管标识的自动化帐户来执行 runbook，请参阅

> [!div class="nextstepaction"]
> [教程：使用托管标识创建自动化 PowerShell runbook](../learn/powershell-runbook-managed-identity.md)