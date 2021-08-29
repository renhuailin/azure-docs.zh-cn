---
title: Azure 与 Logz.io 集成的单一登录 - Azure 合作伙伴解决方案
description: 了解如何为 Azure 与 Logz.io 的集成设置单一登录。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 07/28/2021
ms.author: tomfitz
ms.openlocfilehash: 6ca8cfa3b41ef3cdc6a4030980bc73679b9635aa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778332"
---
# <a name="set-up-logzio-single-sign-on"></a>设置 Logz.io 单一登录

本文介绍如何在 Azure Active Directory 中设置单一登录 (SSO)。 Logz.io 集成的 SSO 是可选的。

## <a name="configure-single-sign-on"></a>配置单一登录

若要在 Logz.io 资源中使用安全断言标记语言 (SAML) SSO 功能，必须设置企业应用程序。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 从门户菜单中，选择“Azure Active Directory”，或搜索“Azure Active Directory”。
1. 转到“管理” > “企业应用程序”，然后选择“新建应用程序”按钮  。
1. 搜索“Logz.io”并选择名为“Logz.io - Azure AD 集成”的 SAML 应用程序，然后选择“创建” 。

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="浏览 Azure Active Directory 库。":::

1. 从“概述”中，复制 SSO 应用程序的应用程序 ID 。

   请记下该应用程序 ID，因为在后面的步骤中将会用它来配置 SAML。

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="复制应用程序 ID。":::

1. 选择名为“2. 设置单一登录”的磁贴。

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="设置单一登录。":::

1. 选择“SAML”协议作为单一登录方法。

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="SAML 单一登录。":::

1. 在 SAML 配置中，选择现有的值，或使用文本框来输入必填字段“标识符”和“回复 URL” 。 若要创建新条目，请使用在前面步骤中复制的应用程序 ID。

   使用以下模式来添加新值：

   - 标识符：`urn:auth0:logzio:<Application ID>`
   - **回复 URL**：`https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="基本 SAML 配置。":::

1. 从“保存单一登录设置”提示选择“是”，然后选择“保存”  。

   对于 SSO 提示，请选择“否，我以后再测试”。

1. 在保存 SAML 配置后，转到“管理” > “属性”，并将“需要使用用户分配?”设置为“否”   。 选择“保存”。

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="单一登录属性。":::

## <a name="next-steps"></a>后续步骤

- 若要解决 SSO 的问题，请参阅[故障排除](troubleshoot.md)。
- 若要创建 Logz.io 集成，请参阅[快速入门：在 Azure 门户中创建 Logz.io 资源](create.md)。
