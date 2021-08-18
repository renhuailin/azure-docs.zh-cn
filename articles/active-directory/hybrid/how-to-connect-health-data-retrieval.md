---
title: Azure AD Connect Health 数据检索说明 |Microsoft Docs
description: 本页介绍了如何从 Azure AD Connect Health 中检索数据。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435cc73d9c2a91a9c72bf07258a15dced581d580
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110054"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Connect Health 数据检索说明

本文档介绍了如何使用 Azure AD Connect 从 Azure AD Connect Health 中检索数据。

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>检索配置了运行状况警报的所有用户的电子邮件地址。

若要检索在 Azure AD Connect Health 中配置为接收警报的所有用户的电子邮件地址，请按以下步骤操作。

1.  首先，转到“Azure Active Directory Connect Health”边栏选项卡，并选择左侧导航栏中的“同步服务”。
 ![同步服务](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  单击“警报”磁贴。</br>
 ![Alert](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  单击“通知设置”。
 ![通知](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  在“通知设置”边栏选项卡中，可以找到已启用为运行状况警报通知接收人的电子邮件地址的列表。
 ![电子邮件](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>检索被标记为 AD FS 错误密码尝试的帐户

若要检索被标记为 AD FS 错误密码尝试的帐户，请按以下步骤操作。

1.  首先，转到“Azure Active Directory Health”边栏选项卡，并选择“同步错误”。
 ![同步错误](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  在“同步错误”边栏选项卡中，单击“导出”。 这会导出已记录的同步错误的列表。
 ![导出](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.yml)
* [Azure AD Connect Health 版本历史记录](reference-connect-health-version-history.md)