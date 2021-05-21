---
title: 澳大利亚和新西兰客户的标识数据存储 - Azure AD
description: 了解 Azure Active Directory 将澳大利亚和新西兰客户的标识相关数据存储在何处。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479060"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中澳大利亚和新西兰客户的标识数据存储

Azure AD 根据组织在订阅 Microsoft 联机服务（如 Microsoft 365 和 Azure）时提供的地址，将标识数据存储在相关地理位置。 有关标识客户数据存储位置的信息，可以使用“Microsoft 信任中心”的“[数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)”部分。

> [!NOTE]
> 集成到 Azure AD 中的服务和应用程序有权访问标识客户数据。 评估使用的每项服务和应用程序，以确定该特定服务和应用程序对标识客户数据的处理方式，以及它们是否符合贵公司的数据存储要求。 有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的数据存储在何处？部分。

对于在澳大利亚或新西兰提供地址的客户，Azure AD 会将这些服务的标识数据保留在澳大利亚数据中心： 
- Azure AD 目录管理 
- 身份验证

所有其他 Azure AD 服务都将客户数据存储在全球数据中心。 要查找服务的数据中心，请参阅“[Azure Active Directory - 数据位于何处？](https://aka.ms/AADDataMap)”

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD 多重身份验证 (MFA)

MFA 将标识客户数据存储在全球数据中心。 有关基于云的 Azure AD MFA 和 Azure MFA 服务器收集和存储用户信息的详细信息，请参阅“[Azure 多重身份验证用户数据收集](../authentication/concept-mfa-data-residency.md)”。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](../authentication/concept-mfa-howitworks.md)
