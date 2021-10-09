---
title: 如何响应适用于 Key Vault 的 Azure Defender 警报
description: 了解响应适用于 Key Vault 的 Azure Defender 发出的警报而所需的步骤。
author: memildin
ms.author: memildin
ms.date: 09/13/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 45ed38f3325ae66c72fff7f0aec35347b5e28ace
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664972"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>响应适用于 Key Vault 的 Azure Defender 警报
当收到来自[适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md) 的警报时，建议按如下所述，调查和响应警报。 适用于 Key Vault 的 Azure Defender 旨在为应用程序和凭据提供保护，因此即使你熟悉触发警报的应用程序或用户，也请基于每个警报验证相关情况，这一点非常重要。  

来自适用于 Key Vault 的 Azure Defender 的警报都包含以下元素：

- 对象 ID
- 可疑资源的用户主体名称或 IP 地址 

根据发生的访问类型，某些字段可能不可用。 例如，如果某个应用程序访问了密钥保管库，你将不会看到关联的用户主体名称。 如果通信来自 Azure 外部，则不会看到对象 ID。

> [!TIP]
> 为 Azure 虚拟机分配 Microsoft IP。 这意味着，警报可能包含 Microsoft IP，即使它与从 Microsoft 外部执行的活动相关。 因此，即使警报包含 Microsoft IP，仍应按照本页描述的内容进行调查。

## <a name="step-1-identify-the-source"></a>步骤 1。 标识源

1. 验证流量是否源自 Azure 租户。 如果已启用密钥保管库防火墙，那么你很可能已经为触发此警报的用户或应用程序提供了访问权限。
1. 如果无法验证流量来源，请继续执行[步骤 2. 做出相应的响应](#step-2-respond-accordingly)。
1. 如果可以确定租户中的流量来源，请与应用程序的用户或所有者联系。 

> [!CAUTION]
> Azure Defender for Key Vault 旨在帮助确定由盗窃的凭据引起的可疑活动。 不要因为识别出了用户或应用程序而消除警报。 与应用程序或用户的所有者联系，并验证活动是否合法。 如果需要，可以创建抑制规则以消除干扰。 在[禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)中了解详情。


## <a name="step-2-respond-accordingly"></a>步骤 2。 做出相应的响应 
如果无法识别用户或应用程序，或者认为不应授予访问权限，请执行以下操作：

- 如果流量来自无法识别的 IP 地址：
    1. 如[配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/general/network-security.md)中所述，启用 Azure Key Vault 的防火墙。
    1. 为受信任的资源和虚拟网络配置防火墙。

- 如果警报来源是未经授权的应用程序或可疑用户：
    1. 打开密钥保管库的访问策略设置。
    1. 删除相应的安全主体，或限制安全主体可执行的操作。  

- 如果警报来源在租户中具有 Azure Active Directory 角色：
    1. 请与您的管理员联系。
    1. 确定是否需要减少或撤销 Azure Active Directory 权限。

## <a name="step-3-measure-the-impact"></a>步骤 3。 度量影响
缓解事件影响后，调查密钥保管库中受影响的机密：
1. 打开 Azure Key Vault 上的“安全性”页并查看触发的警报。
1. 选择已触发的特定警报。
    查看已访问的机密列表和时间戳。
1. 或者，如果已启用密钥保管库诊断日志，可查看前面的操作，以获取相应的调用方 IP、用户主体或对象 ID。  

## <a name="step-4-take-action"></a>步骤 4。 执行操作 
在编译了可疑用户或应用程序访问的机密、密钥和证书的列表后，应立即轮换这些对象。

1. 应禁用或从密钥保管库中删除受影响的机密。
1. 如果凭据用于特定的应用程序：
    1. 联系应用程序的管理员，请他们审核他们的环境，看看是否使用了已泄露的凭据。
    1. 如果使用了已泄露的凭据，则应用程序所有者应确定已访问的信息并降低影响。


## <a name="next-steps"></a>后续步骤

此页介绍了响应来自适用于 Key Vault 的 Azure Defender 的警报的过程。 如需相关信息，请参阅以下页面：

- [用于 Key Vault 的 Azure Defender 简介](defender-for-key-vault-introduction.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)
- [连续导出安全中心数据](continuous-export.md)