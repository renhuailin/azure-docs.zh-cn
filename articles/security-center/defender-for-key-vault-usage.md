---
title: 如何响应适用于 Key Vault 的 Azure Defender 警报
description: 了解响应适用于 Key Vault 的 Azure Defender 发出的警报而所需的步骤。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96122205"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>响应适用于 Key Vault 的 Azure Defender 警报
当收到来自适用于 Key Vault 的 Azure Defender 的警报时，建议按如下所述，调查和响应警报。 适用于 Key Vault 的 Azure Defender 旨在为应用程序和凭据提供保护，因此即使你熟悉触发警报的应用程序或用户，也请基于每个警报验证相关情况，这一点非常重要。  

来自 Azure Defender for Key Vault 的每个警报都包含以下元素：

- 对象 ID
- 可疑资源的用户主体名称或 IP 地址

> [!TIP]
> 根据发生的访问类型，某些字段可能不可用。 例如，如果某个应用程序访问了密钥保管库，你将不会看到关联的用户主体名称。 如果通信来自 Azure 外部，则不会看到对象 ID。

## <a name="step-1-contact"></a>步骤 1。 联系人

1. 验证流量是否源自 Azure 租户。 如果已启用密钥保管库防火墙，则很可能是你已对触发此警报的用户或应用程序提供了访问权限。
1. 如果您无法验证流量来源，请继续执行[步骤 2.即时缓解措施](#step-2-immediate-mitigation)。
1. 如果你可以标识租户中的流量源，请与应用程序的用户或所有者联系。 

> [!CAUTION]
> Azure Defender for Key Vault 旨在帮助确定由盗窃的凭据引起的可疑活动。 不要因为识别出了用户或应用程序而消除警报。 与应用程序或用户的所有者联系，并验证活动是否合法。 如果需要，可以创建抑制规则以消除干扰。 在[禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)中了解详情。


## <a name="step-2-immediate-mitigation"></a>步骤 2. 即时缓解措施 
如果你不认识用户或应用程序，或者你认为不应授予访问权限：

- 如果流量来自无法识别的 IP 地址：
    1. 如[配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/general/network-security.md)中所述，启用 Azure Key Vault 的防火墙。
    1. 为防火墙配置受信任的资源和虚拟网络。

- 如果警报的源是未授权的应用程序或可疑用户：
    1. 打开密钥保管库的访问策略设置。
    1. 删除相应的安全主体，或限制安全主体可执行的操作。  

- 如果警报的源在你的租户中具有 Azure Active Directory 角色：
    1. 请与您的管理员联系。
    1. 确定是否需要减少或撤消 Azure Active Directory 权限。

## <a name="step-3-identify-impact"></a>步骤 3. 确定影响 
缓解了影响后，调查密钥保管库中受影响的机密：
1. 打开 Azure Key Vault 上的“安全性”页并查看触发的警报。
1. 选择已触发的特定警报。
    查看已访问的机密和时间戳的列表。
1. 或者，如果已启用密钥保管库诊断日志，请查看前面的操作，以获取相应的调用方 IP、用户主体或对象 ID。  

## <a name="step-4-take-action"></a>步骤 4. 执行操作 
在编译了可疑用户或应用程序访问的机密、密钥和证书的列表后，应立即轮换这些对象。

1. 应禁用或删除密钥保管库中受影响的机密。
1. 如果凭据用于特定的应用程序：
    1. 请与应用程序的管理员联系，并要求他们审核其环境，以了解自从凭据泄露之后泄露凭据的所有使用情况。
    1. 如果使用了泄露的凭据，则应用程序所有者应该确定已被访问的信息并减轻影响。


## <a name="next-steps"></a>后续步骤

此页介绍了响应来自适用于 Key Vault 的 Azure Defender 的警报的过程。 如需相关信息，请参阅以下页面：

- [用于 Key Vault 的 Azure Defender 简介](defender-for-key-vault-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)