---
title: Azure AD 登录日志中的基本信息标识符 | Microsoft Docs
description: 了解基本信息标识符的用途。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367357"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>Azure AD 登录日志中的基本信息标识符

Azure AD 将所有登录记录到 Azure 租户中以实现符合性。 IT 管理员需要了解登录日志中的值的含义，以便可以正确解释日志值。
本文介绍登录日志的“基本信息”选项卡上的标识符。

## <a name="unique-identifiers"></a>唯一标识符 

在 Azure AD 中，资源访问具有三个相关组件：

- 用户 - 执行登录的身份（用户）。 
- 方式 – 用于访问的客户端（应用程序）。  
- 对象 – 身份访问的目标（资源）。


每个组件都有一个关联的唯一标识符 (ID)。 以下是使用 Windows Azure 服务管理 API 访问 Azure 门户的用户示例。

![打开审核日志](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>租户标识符

登录日志跟踪两个租户标识符：

- 主租户 – 拥有用户身份的租户。 
- 资源租户 – 拥有（目标）资源的租户。

这些标识符与跨租户方案相关。 例如，要了解租户外部的用户如何访问资源，请选择主租户与资源租户不匹配的所有条目。

## <a name="request-id"></a>请求 ID

请求 ID 是对应于已颁发令牌的标识符。 如果你正在查找使用特定令牌执行的登录，则需要先从令牌中提取请求 ID。


## <a name="correlation-id"></a>相关性 ID

相关性 ID 是将来自同一登录会话的登录关联起来的标识符。 为方便起见，已实现标识符。 不保证其准确度，因为该值基于客户端传递的参数。 




## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 中的登录日志](concept-sign-ins.md)
* [什么是 Azure AD 中的登录诊断？](overview-sign-in-diagnostics.md)
