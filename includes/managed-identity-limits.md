---
title: include 文件
description: include 文件
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201273"
---
- 按 [Azure AD 服务限制和局限性](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md)中所述，每个托管标识都计入 Azure AD 租户中的对象配额限制。
-   托管标识的创建速率有以下限制：

    1. 每个 Azure 区域每个 Azure AD 租户：每 20 秒 400 次创建操作。
    2. 每个 Azure 区域每个 Azure 订阅：每 20 秒 80 次创建操作。

-   可使用 Azure 资源分配用户分配的托管标识的速率：

    1. 每个 Azure 区域每个 Azure AD 租户：每 20 秒 400 次分配操作。
    2. 每个 Azure 区域每个 Azure 订阅：每 20 秒 300 次分配操作。




