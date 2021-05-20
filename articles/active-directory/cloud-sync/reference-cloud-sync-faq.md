---
title: Azure AD Connect 云同步常见问题解答
description: 本文档描述有关云同步的常见问题。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613030"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Azure Active Directory Connect 云同步常见问题解答

阅读有关 Azure Active Directory (Azure AD) Connect 云同步的常见问题。

## <a name="general-installation"></a>常规安装

**问：云同步的运行频率如何？**

云预配计划为每 2 分钟运行一次。 任何用户、组和密码哈希更改每隔 2 分钟预配到 Azure AD。

**问：首次运行时出现密码哈希同步失败。为什么？**

这是正常情况。 失败的原因是 Azure AD 中不存在相关的用户对象。 将用户预配到 Azure AD 后，密码哈希应会在后续的运行中预配。 请等待运行几次，然后确认密码哈希同步是否不再出错。

**问：如果 Active Directory 实例具有云同步不支持的属性（例如，目录扩展），会发生什么情况？**

云预配将运行并预配受支持的属性。 不支持的属性将不会预配到 Azure AD。 查看 Active Directory 中的目录扩展，确保你不需要将这些属性预配到 Azure AD。 如果需要一个或多个属性，请考虑使用 Azure AD Connect 同步或将所需的信息移到支持的属性之一（例如，扩展属性 1-15）。

**问：Azure AD Connect 同步与云同步之间有何区别？**

使用 Azure AD Connect sync 时，预配将在本地同步服务器上运行。 配置存储在本地同步服务器上。 使用 Azure AD Connect 云同步时，预配配置将存储在云中，并作为 Azure AD 预配服务的一部分在云中运行。 

**问：是否可以使用云同步从多个 Active Directory 林同步？**

是。 可以使用云预配从多个 Active Directory 林进行同步。 在多林环境中，所有引用（例如管理器）需位于域中。  

**问：如何更新代理？**

Microsoft 会自动升级代理。 对于 IT 团队来说，这可以减轻必须测试和验证新代理版本的负担。 

**问：是否可以禁用自动升级？**

没有任何支持的方法可以禁用自动升级。

**问：是否可以更改云同步的源定位点？**

默认情况下，云同步使用 ms-ds-consistency-GUID，并可回退到用作源定位点的 ObjectGUID。 没有任何支持的方法可以更改源定位点。

**问：使用云同步时，我看到了使用 AD 域名的新服务主体。这是否符合预期？**

是，云同步将使用域名作为服务主体名称，为预配配置创建服务主体。 请不要对服务主体配置进行任何更改。

**问：如果已同步的用户在下次登录时必须更改密码，将会发生什么情况？**

如果在云同步中启用了密码哈希同步，并且要求已同步用户在本地 AD 中下次登录时更改密码，则云同步不会将“要更改的”密码哈希预配到 Azure AD。 用户更改密码后，用户密码哈希将从 AD 预配到 Azure AD。

**问：云同步是否支持写回任何对象的 ms-ds-consistencyGUID？**

否，云同步不支持写回任何对象（包括用户对象）的 ms-ds-consistencyGUID。 

**问：我正使用云同步来预配用户。我删除了配置。为何我仍在 Azure AD 中看到了旧的同步对象？** 

删除配置时，云同步不会自动删除 Azure AD 中的已同步对象。 若要确保删除旧对象，请将配置范围更改为空的组或组织单位。 预配运行并清理对象后，请禁用并删除配置。 

**问：不支持 Exchange 混合意味着什么？**

Exchange 混合部署功能使 Exchange 邮箱能够在本地和 Microsoft 365 中共存。 Azure AD Connect 将特定的属性集从 Azure AD 同步回到本地目录。  云预配代理目前不将这些属性同步回本地目录，因此不支持用它替代 Azure AD Connect。

**问：能否在 Windows Server Core 上安装云预配代理？**

否，不支持在 Server Core 上安装代理。

**问：云预配代理是否可以与暂存服务器一起使用？**

否，不支持暂存服务器。

**问：是否可以同步来宾用户帐户？**

否，不支持同步来宾用户帐户。

**问：如果我将用户从限定为云同步范围的 OU 移到限定为 Azure AD Connect 范围的 OU，会发生什么情况？**

将删除并重新创建该用户。  如果将用户从限定为云同步范围的 OU 中移出，则会将此操作视为删除操作。  如果将用户移动到 Azure AD Connect 管理的 OU，则会将其重新预配到 Azure AD 并创建新用户。

**问：如果重命名或移动云同步筛选器范围内的 OU，在 Azure AD 中创建的用户会发生什么情况？**

无变化。  如果重命名或移动该 OU，不会删除用户。

**问：Azure AD Connect 云同步是否支持大型组？**

是。 如今，我们最多支持使用 OU 范围筛选进行同步的 50K 组成员。 同时，当你使用组范围筛选时，建议将组大小保留为小于 1500 成员。 出现这种情况的原因是，即使你可以将大型组作为组范围筛选器的一部分进行同步，但当你按大于 1500 的批次将成员添加到该组时，增量同步将会失败。 

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
