---
title: 用于将应用迁移到 Azure Active Directory 的资源 | Microsoft Docs
description: 帮助你将应用程序访问和身份验证迁移到 Azure Active Directory (Azure AD) 的资源。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 05c7b2f668565fe4ab37ed01ad65bef7cb6d95d6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437915"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>用于将应用程序迁移到 Azure Active Directory 的资源

帮助你将应用程序访问和身份验证迁移到 Azure Active Directory (Azure AD) 的资源。

| 资源  | 说明  |
|:-----------|:-------------|
|[将应用迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper) | 本白皮书介绍了迁移的优势，并介绍了如何在四个明确概述的阶段中规划迁移：发现、分类、迁移和持续管理。 本文将介绍如何思考该进程并将项目分解为易于使用的部分。 整个文档为指向重要资源的链接，可全程为你提供帮助。 |
|[解决方案指南：将应用从 Active Directory 联合身份验证服务 (AD FS) 迁移到 Azure AD](./migrate-adfs-apps-to-azure.md) | 本解决方案指南逐步说明规划和执行迁移白皮书中更高级别描述的应用程序迁移项目的四个相同阶段。 在本指南中，你将学习如何将这些阶段应用于将应用程序从 Active Directory 联合身份验证服务 (AD FS) 移动到 Azure AD 的特定目标。|
|[开发人员指南：面向开发人员的 AD FS 到 Azure AD 的应用程序迁移 playbook](https://aka.ms/adfsplaybook) | 这组 ASP.NET 的代码示例和随附的教程将帮助你了解如何安全可靠地将集成了 Active Directory 联合身份 (AD FS) 的应用程序迁移到 Azure Active Directory (Azure AD)。 本教程重点面向以下开发人员：不仅需要了解在 AD FS 和 Azure AD 上配置应用，还会在此过程中认识并自信其代码库将需要的更改。|
| [工具：Active Directory 联合身份验证服务迁移就绪性脚本](https://aka.ms/migrateapps/adfstools) | 这是可以在本地 Active Directory 联合身份验证服务 (AD FS) 服务器上运行的脚本，用于确定应用迁移到 Azure AD 的就绪情况。|
| [部署计划：从 AD FS 迁移到密码哈希同步](https://aka.ms/ADFSTOPHSDPDownload) | 凭借密码哈希同步，可将用户密码的哈希从本地 Active Directory 同步到 Azure AD。 通过该操作，Azure AD 可对用户进行身份验证，而无需与本地 Active Directory 进行交互。|
| [部署计划：从 AD FS 迁移到直通身份验证](https://aka.ms/ADFSTOPTADPDownload)|借助 Azure AD 直通身份验证，用户可使用相同的密码登录到本地应用程序和基于云的应用程序。 此功能可为你的用户提供更好的体验，因为他们只需要记住一个密码。 它还可降低 IT 支持人员成本，因为用户只需要记住一个密码就不会忘记如何登录。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。|
| [部署计划：使用 Azure AD 启用 SaaS 应用程序的单一登录](https://aka.ms/SSODPDownload) | 进行单一登录 (SSO) 时，只需使用单个用户帐户登录一次，就能访问开展业务所需的全部应用和资源。 例如，用户登录后，该用户可从 Microsoft Office 移动到 SalesForce 和 Box，而无需再次进行身份验证（例如，键入密码）。
| [部署计划：使用应用程序代理将应用扩展到 Azure AD](https://aka.ms/AppProxyDPDownload)| 传统上，提供从员工笔记本电脑和其他设备到本地应用程序的访问权限会涉及虚拟专用网络 (VPN) 或外围安全区域 (DMZ)。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。 使用 Azure AD 应用程序代理可更轻松地访问本地应用程序。 |
| [部署计划](../fundamentals/active-directory-deployment-plans.md) | 查找更多部署计划，以部署多种功能，如多重身份验证、条件访问、用户预配、无缝 SSO、自助式密码重置，等等！ |
| [将应用从 Symantec SiteMinder 迁移到 Azure AD](https://azure.microsoft.com/mediahandler/files/resourcefiles/migrating-applications-from-symantec-siteminder-to-azure-active-directory/Migrating-applications-from-Symantec-SiteMinder-to-Azure-Active-Directory.pdf) | 通过示例获取有关应用程序迁移和集成选项的分步指南，该指南指导你完成将应用程序从 Symantec SiteMinder 迁移到 Azure AD 的过程。 |
| [将应用从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md) | 获取有关将应用程序从 Okta 迁移到 Azure AD 的分步指导。 |
| [将 Okta 联合身份验证迁移到 Azure AD 托管身份验证](migrate-okta-federation-to-azure-active-directory.md) | 了解如何将现有的 Office 365 租户与 Okta 进行联合以实现单一登录功能。 |
| [将 Okta 同步预配迁移到基于 Azure AD Connect 的同步](migrate-okta-sync-provisioning-to-azure-active-directory.md) | 当前使用从 Okta 到 Azure AD 的用户预配并正在将用户同步或通用同步迁移到 Azure AD Connect 的组织可参考的分步指导。 |
| [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md) | 获取有关如何从 Okta 中的全局或应用程序级登录策略迁移到 Azure AD 条件访问策略，以便在 Azure AD 和连接的应用程序中保护用户访问的分步指导。 |

