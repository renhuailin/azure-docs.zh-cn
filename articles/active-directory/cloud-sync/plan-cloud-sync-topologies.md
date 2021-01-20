---
title: Azure AD Connect 云同步支持的拓扑和方案
description: 了解使用 Azure AD Connect 云同步 Azure AD) 拓扑的各种本地和 Azure Active Directory (。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613033"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect 云同步支持的拓扑和方案
本文介绍使用 Azure AD Connect 云同步 Azure AD) 拓扑的各种本地和 Azure Active Directory (。本文仅包括支持的配置和方案。

> [!IMPORTANT]
> Microsoft 不支持在正式记录的配置或操作之外修改或操作 Azure AD Connect 云同步。 任何这些配置或操作都可能会导致不一致或不受支持的 Azure AD Connect 云同步状态。因此，Microsoft 无法为此类部署提供技术支持。

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>有关所有方案和拓扑的注意事项
下面是选择解决方案时要记住的信息列表。

- 必须跨所有林唯一标识用户和组
- 云同步不发生跨林匹配
- 用户或组必须在所有林中仅表示一次
- 将自动选择对象的源定位点。  它使用 Msds-consistencyguid （如果存在），否则使用 ObjectGUID。
- 不能更改用于源锚点的属性。

## <a name="single-forest-single-azure-ad-tenant"></a>单个林，单个 Azure AD 租户
![显示单个林和单个租户的拓扑的关系图。](media/tutorial-single-forest/diagram-2.png)

最简单的拓扑是具有一个或多个域的单个本地林，以及单个 Azure AD 租户。  有关此方案的示例，请参阅 [教程：包含单个 Azure AD 租户的单个林](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>多林、单个 Azure AD 租户
![多林和单个租户的拓扑](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

常见拓扑是一个多个 AD 林，其中包含一个或多个域，一个 Azure AD 租户。  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>具有 Azure AD Connect 的现有林，具有云预配的新林
![显示现有林和新林的拓扑的关系图。](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

这种情况下，拓扑类似于多林方案，但这种情况涉及到现有 Azure AD Connect 环境，然后使用 Azure AD Connect 云同步引入新的林。 有关此方案的示例，请参阅 [教程：包含单个 Azure AD 租户的现有林](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>试验 Azure AD Connect 现有混合 AD 林中的云同步
![单个林和单个租户 ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) 的拓扑，试验方案涉及在同一林中同时存在 Azure AD Connect 和 Azure AD Connect 云同步，并相应地限定用户和组。 注意：对象只应位于其中一个工具的范围内。 

有关此方案的示例，请参阅 [教程：试点 Azure AD Connect 现有已同步的 AD 林中的云同步](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)

