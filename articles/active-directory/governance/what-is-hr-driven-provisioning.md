---
title: 使用 Azure Active Directory 进行的 HR 驱动的预配是什么？ | Microsoft Docs
description: 提供 HR 驱动的预配的概述。
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
ms.openlocfilehash: 18e1e2548566fa3caa4bbdeb72b9fd9c14d06cff
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428204"
---
# <a name="what-is-hr-driven-provisioning"></a>HR 驱动的预配是什么？

![HR 预配](./media/what-is-hr-driven-provisioning/cloud2a.png)

HR 驱动的预配是基于人力资源系统创建数字标识的过程。  HR 系统成为这些新创建的数字标识的授权起始点，并且通常是许多预配过程的起点。  例如，如果有新员工进入公司，则会在人力资源系统中创建他们的档案。  此创建操作会触发将用户帐户预配到 Active Directory，然后 Azure AD Connect 会将此帐户预配到 Azure AD，等等。

HR 驱动的预配可以是基于本地的，也可以是基于云的。

## <a name="on-premises-based-hr-provisioning"></a>基于本地的 HR 预配
基于本地的 HR 预配是通过使用本地 HR 系统和一种预配新的数字标识的方式完成的。

HR 系统具有各种包、软件捆绑包，并且可以使用 SQL Server、LDAP 目录等。

目前，当在这些 HR 系统中创建新标识时，Microsoft 本地 HR 预配解决方案使用 Microsoft Identity Manager 触发预配。

使用 MIM，你可以将用户从本地 HR 系统预配到 Active Directory 或 Azure AD。

有关 Microsoft Identity Manager 及其支持的系统的信息，请参阅 [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) 文档。

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>后续步骤 
- [标识生命周期管理是什么](what-is-identity-lifecycle-management.md)
- [什么是预配？](what-is-provisioning.md)
- [应用预配是什么？](what-is-app-provisioning.md)
- [目录间预配是什么？](what-is-inter-directory-provisioning.md)