---
title: 使用 Azure Active Directory 进行的目录间预配是什么？ | Microsoft Docs
description: 提供标识目录间预配的概述。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: compliance
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e429310b617002a8c4d2eac414541b288197ca
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428569"
---
# <a name="what-is-inter-directory-provisioning"></a>目录间预配是什么？

目录是用于查找、控制、管理和组织项目和网络资源的共享信息基础结构。  使用目录服务的应用程序示例是 Microsoft Active Directory 和 Azure AD。  标识可帮助目录系统做出决策，例如决定谁有权访问哪些内容以及谁可以使用特定资源。

目录间预配是指在两个不同的目录服务系统之间预配标识。   目录间预配的最常见场景是将已在 Active Directory 中的用户预配到 Azure AD 中。 此预配操作可通过 Azure AD Connect 同步或 Azure AD Connect 云配置等代理来完成。

通过目录间预配，我们可以创建[混合标识](../hybrid/whatis-hybrid-identity.md)环境。


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Azure AD 支持哪种类型的目录间预配

Azure AD 目前支持采用三种方法来完成目录间预配。 这些方法包括：

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - 一种 Microsoft 工具，旨在满足和实现混合标识，包括从 Active Directory 到 Azure AD 的目录间预配。

- [Azure AD Connect 云配置](../cloud-sync/what-is-cloud-sync.md) - 一个新的 Microsoft 代理，旨在满足和实现混合标识目标。  它提供在 Active Directory 和 Azure AD 之间进行目录间预配的简单体验。

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) - Microsoft 的本地标识和访问管理解决方案，可帮助管理组织内的用户、凭据、策略和访问权限。 此外，MIM 还提供高级目录间预配，以实现 Active Directory、Azure AD 和其他目录的混合标识环境。

### <a name="key-benefits"></a>主要优点

目录间预配的这一功能提供了以下重要的业务优势：

- [密码哈希同步](../hybrid/whatis-phs.md) - 一种登录方法，它将用户的本地 AD 密码与 Azure AD 进行同步。
- [直通身份验证](../hybrid/how-to-connect-pta.md) - 另一种登录方法，它允许用户在本地和云中使用相同的密码，但不要求额外提供联合环境的基础结构。
- [联合身份验证集成](../hybrid/how-to-connect-fed-whatis.md) - 可用于使用本地 AD FS 基础结构配置混合环境。 它还提供了 AD FS 管理功能，例如证书续订和其他 AD FS 服务器部署。
- [同步](../hybrid/how-to-connect-sync-whatis.md) - 负责创建用户、组和其他对象。  另外，它还负责确保本地用户和组的标识信息与云匹配。  此同步还包括密码哈希。
- [运行状况监视](../hybrid/whatis-azure-ad-connect.md) - 可提供可靠监视，并可在 Azure 门户中提供一个中心位置，用于查看此活动。 


## <a name="next-steps"></a>后续步骤 
- [标识生命周期管理是什么](what-is-identity-lifecycle-management.md)
- [什么是预配？](what-is-provisioning.md)
- [HR 驱动的预配是什么？](what-is-hr-driven-provisioning.md)
- [应用预配是什么？](what-is-app-provisioning.md)