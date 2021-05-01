---
title: Microsoft 365 政府版中有哪些针对 Azure AD 的新增功能？ | Microsoft Docs
description: 了解 Microsoft 365 政府版云实例中对 Azure Active Directory (Azure AD) 的一些更改，这些更改可能会影响你。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89318924"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府版中适用于 Azure Active Directory 的新增功能

我们在 Microsoft 365 政府版云实例中对 Azure Active Directory (Azure AD) 进行了一些更改，适用于使用以下服务的客户：

- Microsoft Azure 政府

- Microsoft 365 政府版 - GCC High

- Microsoft 365 政府版 - DoD

本文不适用于 Microsoft 365 政府版 - GCC 客户。

## <a name="changes-to-the-initial-domain-name"></a>对初始域名的更改

在你的组织首次注册 Microsoft 365 政府版联机服务的过程中，系统会要求你选择组织的域名 `<your-domain-name>.onmicrosoft.com`。 如果你已有了一个后缀为“.com”的域名，则不会有任何更改。

但是，如果要注册新的 Microsoft 365 政府版服务，系统会要求你选择一个使用 `.us` 后缀的域名。 因此，它将为 `<your-domain-name>.onmicrosoft.us`。

>[!Note]
>此更改不适用于由云服务提供商 (CSP) 管理的客户。

## <a name="changes-to-portal-access"></a>对门户访问的更改

我们已更新了 Microsoft Azure 政府版、Microsoft 365 政府版 - GCC High 和 Microsoft 365 政府版 - DoD 的门户终结点，如[终结点映射表](#endpoint-mapping)中所示。

以前，客户可以使用全球 Azure 门户 (portal.azure.com) 和 Office 365 门户 (portal.office.com) 登录。 进行此更新后，客户现在必须使用特定 Microsoft Azure 政府版门户、Microsoft 365 政府版 - GCC High 门户、Microsoft 365 政府版 - DoD 门户登录。

## <a name="endpoint-mapping"></a>终结点映射

下表显示了适用于所有客户的终结点：

| 名称 | 终结点详细信息 |
|------|------------------|
| 门户 |Microsoft Azure 政府版： https://portal.azure.us<p>Microsoft 365 政府版 - GCC High： https://portal.office365.us<p>Microsoft 365 政府版 - DoD： https://portal.apps.mil |
| Azure Active Directory 颁发机构终结点 | https://login.microsoftonline.us |
| 适用于 Microsoft 365 政府版 - GCC High 的 Microsoft Graph API | https://graph.microsoft.us |
| 适用于 Microsoft 365 政府版 - DoD 的 Microsoft Graph API | https://dod-graph.microsoft.us |
| Azure 政府版服务终结点 | 有关详细信息，请参阅 [Azure 政府版开发人员指南](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 政府版 - GCC High 终结点 | 有关详细信息，请参阅 [Office 365 美国政府版 GCC High 终结点](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 政府版 - DoD | 有关详细信息，请参阅 [Office 365 美国政府版 DoD 终结点](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [什么是 Azure 政府？](../../azure-government/documentation-government-welcome.md)

- [Azure 政府版 AAD 颁发机构终结点更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美国政府云中的 Microsoft Graph 终结点](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 美国政府版 GCC High 和 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)