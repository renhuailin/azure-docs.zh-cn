---
title: Azure Active Directory 操作参考指南
description: 本操作参考指南介绍应采取哪些检查与操作来保护和维护标识和访问管理、身份验证、治理和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "74535309"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 操作参考指南

本操作参考指南介绍了为保护和维护以下领域而要执行的检查和措施：

- **[标识和访问管理](active-directory-ops-guide-iam.md)** - 能够管理标识及其权利的生命周期。
- **[身份验证管理](active-directory-ops-guide-auth.md)** - 能够管理凭据、定义身份验证体验、委派分配、衡量使用情况，并根据企业安全状况定义访问策略。
- **[治理](active-directory-ops-guide-govern.md)** - 能够评估和证明已授予非特权标识和特权标识的权限、审核和控制环境更改。
- **[操作](active-directory-ops-guide-ops.md)** - 优化 Azure Active Directory (Azure AD) 的操作。

这里的部分建议可能并不适用于所有客户环境，例如，如果组织使用密码哈希同步，则 AD FS 最佳做法可能并不适用。

> [!NOTE]
> 这些建议截至发布之日为最新，但可能会随着时间而改变。 组织应持续评估其标识实践，因为 Microsoft 产品和服务会不断发展。 组织订阅其他 Azure AD Premium 许可证时，建议可能会发生变化。 例如，Azure AD Premium P2 将包含更多治理建议。

## <a name="stakeholders"></a>利益干系人

本参考指南的每个部分均建议分配利益干系人以成功计划和执行关键任务。 下表列出了本指南中的所有利益干系人：

| 利益干系人 | 说明 |
| :- | :- |
| IAM 运营团队 | 该团队负责管理标识和访问管理系统的日常操作 |
| 生产力团队 | 该团队拥有并管理生产力应用程序，例如电子邮件、文件共享和协作、即时消息和会议。 |
| 应用程序所有者 | 从组织的业务角度和技术角度来看，该团队拥有特定的应用程序。 |
| InfoSec 体系结构团队 | 该团队计划并设计组织的信息安全做法。 |
| InfoSec 运营团队 | 该团队运行和监视 InfoSec 体系结构团队实现的信息安全做法。 |

## <a name="next-steps"></a>后续步骤

[标识和访问管理检查和操作](active-directory-ops-guide-iam.md)入门。
