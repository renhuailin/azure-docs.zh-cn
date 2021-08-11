---
title: 用于 Azure Active Directory 应用程序管理的 PowerShell 示例
description: 这些 PowerShell 示例用于在 Azure Active Directory 租户中管理的应用。 可以使用这些示例脚本来查找有关机密和证书的过期信息。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: davidmu
ms.reviewer: mifarca
ms.openlocfilehash: c0e6969b0d6814e74e99a6ad893284bc407b269d
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568417"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>应用程序管理的 Azure Active Directory PowerShell 示例

下表包含 Azure AD 应用程序管理的 PowerShell 脚本示例的链接。 这些示例需要：

- [适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2)或
- [适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)，除非另有说明。

有关这些示例中使用的 cmdlet 的更多信息，请参阅[应用程序](/powershell/module/azuread/#applications)。

| 链接 | 说明 |
|---|---|
|**应用程序管理脚本**||
| [导出机密和证书（应用注册）](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | 导出 Azure Active Directory 租户中应用注册的机密和证书。 |
| [导出机密和证书（企业应用）](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | 导出 Azure Active Directory 租户中企业应用的机密和证书。 |
| [导出即将过期的机密和证书](scripts/powershell-export-apps-with-expriring-secrets.md) | 在 Azure Active Directory 租户中导出包含即将过期的机密和证书的应用注册及其所有者。 |
| [导出要求的日期后即将过期的机密和证书](scripts/powershell-export-apps-with-secrets-beyond-required.md) | 导出其机密和证书在 Azure Active Directory 租户中超出所需日期的应用注册。 这将使用非交互式 Client_Credentials OAuth 流。 |
