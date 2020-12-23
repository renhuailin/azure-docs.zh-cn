---
title: 在 Azure 安全中心监视标识和访问 | Microsoft Docs
description: 了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: b36f52fd97a9f7ff294f14ec394ff9a9f607b5b0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187147"
---
# <a name="monitor-identity-and-access"></a>监视标识和访问

安全边界已从网络边界演进成标识边界。 通过这种发展，安全性越来越少地与如何保护网络相关，而更多地与如何管理应用、数据和用户的安全性相关。

通过监视与标识相关的活动和配置设置，你可在事件发生之前主动采取措施或事后采取措施来阻止尝试进行的攻击。

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>安全中心提供哪些标识和访问保护？ 

Azure 安全中心具有两个专用的安全控件，用于确保满足组织的标识和安全要求： 

 - **管理访问和权限** - 建议采用 [最小特权访问模式](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)，并确保仅向用户授予完成其工作所需的访问权限。 此控件还包括有关实现 [azure 基于角色的访问控制 (AZURE RBAC) ](../role-based-access-control/overview.md) 以控制对资源的访问权限的建议。
 
 - **启用 MFA** - 启用 [MFA](https://www.microsoft.com/security/business/identity/mfa) 后，帐户将更加安全，用户仍可通过单一登录对几乎所有应用程序进行身份验证。

### <a name="example-recommendations-for-identity-and-access"></a>标识和访问的示例建议

在安全中心的“建议”页上的这两个控件中可能会显示以下示例建议：

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 只多只为订阅指定 3 个所有者
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除弃用的帐户（弃用的帐户是不再需要且被 Azure Active Directory 阻止登录的帐户）

> [!TIP]
> 有关这些建议以及可能会在这些控件中看到其他建议的详细信息，请参阅[标识和访问建议](recommendations-reference.md#recs-identity)。

### <a name="limitations"></a>限制

安全中心的标识和访问保护存在一些限制：

- 标识建议不适用于拥有超过 600 个帐户的订阅。 在这种情况下，这些建议将在“不可用的评估”下列出。
- 标识建议不适用于云解决方案提供商 (CSP) 合作伙伴的管理代理。
- 标识建议不标识使用特权标识管理 (PIM) 系统管理的帐户。 如果你使用的是 PIM 工具，你可能会在 **管理访问权限和权限** 控制中看到不准确的结果。

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>多重身份验证 (MFA) 和 Azure Active Directory 

启用 MFA 需要 [Azure Active Directory (AD) 租户权限](../active-directory/roles/permissions-reference.md)。

- 如果拥有高级版 AD，请使用[条件访问](../active-directory/conditional-access/concept-conditional-access-policy-common.md)启用 MFA。
- 如果使用的是 AD 免费版，请按照 [Azure Active Directory 文档](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)中的说明启用“安全默认值”。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>标识未启用多重身份验证 (MFA) 的帐户

若要查看未启用 MFA 的帐户，请使用以下 Azure Resource Graph 查询。 该查询返回建议“应对拥有订阅所有者权限的帐户启用 MFA”的所有运行不正常的资源 - 帐户。 

1. 打开“Azure Resource Graph 资源管理器”。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 输入以下查询并选择“运行查询”。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData` 属性显示未强制启用 MFA 的帐户的帐户对象 ID 列表。 

    > [!NOTE]
    > 这些帐户显示为对象 ID 而不是帐户名称，以保护帐户持有者的隐私。

> [!TIP]
> 或者，可以使用安全中心的 REST API 方法[评估 - 获取](/rest/api/securitycenter/assessments/get)。


## <a name="next-steps"></a>后续步骤
若要详细了解适用于其他 Azure 资源类型的建议，请参阅以下文章：

- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)