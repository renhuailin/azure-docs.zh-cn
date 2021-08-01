---
title: 更改 Azure AD 连接器帐户密码 | Microsoft Docs
description: 本主题介绍如何还原 Azure AD 连接器帐户。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5a16e571478428ef1eb068236369b6e9af9f19e1
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853687"
---
# <a name="change-the-azure-ad-connector-account-password"></a>更改 Azure AD 连接器帐户密码
Azure AD 连接器帐户应该是免费服务。 但如果需要重置其凭据，则可以参阅本主题。 例如，全局管理员错误地使用 PowerShell 对帐户重置了密码。

## <a name="reset-the-credentials"></a>重置凭据
如果 Azure AD 连接器帐户由于身份验证问题无法联系 Azure AD，则可以重置密码。

1. 登录到 Azure AD Connect 同步服务器并打开 PowerShell。
2. 若要提供 Azure AD 全局管理员凭据，请运行 `$credential = Get-Credential`。
3. 运行 cmdlet `Add-ADSyncAADServiceAccount -AADCredential $credential`。

   如果 cmdlet 成功，则会出现 PowerShell 命令提示符。 
   
此 cmdlet 重置服务帐户的密码，并在 Azure AD 和同步引擎中更新该密码。

## <a name="known-issues-these-steps-can-solve"></a>这些步骤可以解决的已知问题
本部分列出了客户报告的可以通过重置 Azure AD 连接器帐户凭据解决的错误。

---
事件 6900 服务器在处理密码更改通知时遇到意外的错误：AADSTS70002：验证凭据时出错。 AADSTS50054：使用了旧密码进行身份验证。

---
事件 659 检索密码策略同步配置时出错。 Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException：AADSTS70002：验证凭据时出错。 AADSTS50054：使用了旧密码进行身份验证。

## <a name="next-steps"></a>后续步骤
**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
