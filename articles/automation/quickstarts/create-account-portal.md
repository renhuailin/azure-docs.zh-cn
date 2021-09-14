---
title: 快速入门 - 使用门户创建 Azure 自动化帐户
description: 本快速入门可帮助你开始使用门户创建 Azure 自动化帐户
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 5d73da041b5ecb5dd2b7089ee898b5bffbe4f512
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542976"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建自动化帐户

可以使用 Azure 门户创建 Azure [自动化帐户](../automation-security-overview.md)。Azure 门户是一个基于浏览器的用户界面，在其中可以访问许多资源。 一个自动化帐户可以跨所有区域和订阅管理给定租户的资源。 本快速入门将引导你创建自动化帐户。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-automation-account"></a>创建自动化帐户

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部菜单中，选择“+ 创建资源”  。

1. 在“类别”下，选择“IT 和管理工具”，然后选择“自动化” 。

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="在门户中查找自动化帐户。":::

1. 在“添加自动化帐户”页面上，提供以下信息：

   | 属性 | 说明 |
   |---|---|
   |名称| 为其位置和资源组输入唯一的名称。 已删除的自动化帐户的名称可能无法立即可用。 在用户界面中输入帐户名称后，无法对其进行更改。 |
   |订阅| 从下拉列表中，选择帐户的 Azure 订阅。|
   |资源组|从下拉列表中选择现有资源组，或选择“新建”  。|
   |位置| 从下拉列表中，选择帐户的位置。 如果需要可以部署自动化帐户的位置的已更新列表，请参阅[可使用的产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)|
   |创建 Azure 运行方式帐户| 请选择“否”。  自动化帐户中的 Azure 运行方式帐户对于向 Azure 进行身份验证很有用；但是现在可以在自动化帐户中使用托管标识。 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md)为应用程序提供一个标识，可以在连接到支持 Azure Active Directory (Azure AD) 身份验证的资源时使用。 |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="用于添加自动化帐户的必填字段":::

1. 选择“创建”以启动自动化帐户部署。 大约一分钟后即可完成创建。

1. 部署完成后，会收到通知。 在通知中选择“转到资源”，打开“自动化帐户”页面 。

1. 查看新的自动化帐户。

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="自动化帐户“概述”页面":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用自动化帐户，请从“概述”页面中选择“删除”，然后在出现提示时选择“是”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个自动化帐户。 若要在自动化帐户中使用托管标识，请继续阅读下一个快速入门：

> [!div class="nextstepaction"]
> [快速入门 - 启用托管标识](enable-managed-identity.md)

