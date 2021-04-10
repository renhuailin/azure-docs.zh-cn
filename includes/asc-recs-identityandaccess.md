---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: d35fbc6f16b018f55e4ec7bcd6257884c3f10186
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466134"
---
这一类别有 21 条相关建议。

|建议 |说明 |严重性 |
|---|---|---|
|只多只为订阅指定 3 个所有者 |为了降低所有者帐户遭受泄露的可能性，建议将所有者帐户的数量限制为最多 3 个<br />（相关策略：[最多只能为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4f11b553-d42e-4e3a-89be-32ca364cad4c)） |高 |
|应启用 Azure Defender for Key Vault |Azure 安全中心包含 Azure Defender for Key Vault，它提供额外的安全情报层。<br>Azure Defender for Key Vault 会检测访问或恶意利用 Key Vault 帐户的异常和可能有害的企图。<br><br>重要说明：修正此建议将导致对密钥保管库的保护产生费用。 如果此订阅中没有任何密钥保管库，则不会产生任何费用。<br>如果将来在此订阅中创建任何密钥保管库，它将自动受到保护，届时将开始计费。<br> <a href='https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault'>详细了解适用于 Key Vault 的 Azure Defender。</a><br />（相关策略：[应启用适用于 Key Vault 的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fmicrosoft.authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)） |高 |
|应从订阅中删除弃用的帐户 |应从订阅中删除已被阻止登录的用户帐户。<br>这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br />（相关策略：[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)） |高 |
|应从订阅中删除拥有所有者权限的已弃用帐户 |应从订阅中删除已被阻止登录的用户帐户。<br>这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br />（相关策略：[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)） |高 |
|应启用 Key Vault 中的诊断日志 |启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。<br />（相关策略：[应启用 Key Vault 中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcf820ca0-f99e-4f3e-84fb-66e913812d21)） |低 |
|应从订阅中删除拥有所有者权限的外部帐户 |应从订阅中删除拥有所有者权限的具有不同域名的帐户（外部帐户）。 这可防止不受监视的访问。 这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br />（相关策略：[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff8456c1c-aa66-4dfb-861a-25d127b775c9)） |高 |
|应从订阅中删除拥有读取权限的外部帐户 |应从订阅中删除拥有读取权限的具有不同域名的帐户（外部账户）。 这可防止不受监视的访问。 这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br />（相关策略：[应从订阅中删除拥有读取权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5f76cf89-fbf2-47fd-a3f4-b891fa780b60)） |高 |
|应从订阅中删除具有写入权限的外部帐户 |应从订阅中删除拥有写入权限的具有不同域名的帐户（外部账户）。 这可防止不受监视的访问。 这些帐户可能会成为攻击者的目标，攻击者会设法在不被发现的情况下访问你的数据。<br />（相关策略：[应从订阅中删除具有写入权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5c607a2e-c700-4744-8254-d77e7c9eb5e4)） |高 |
|应在 Key Vault 上启用防火墙 |密钥保管库防火墙可防止未经授权的流量到达密钥保管库，并为机密提供额外的保护。 启用防火墙可确保只有来自允许的网络的流量可以访问密钥保管库。<br />（相关策略：[应在 Key Vault 上启用防火墙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f55615ac9-af46-4a59-874e-391cc3dfb490)） |中型 |
|Key Vault 密钥应具有到期日期 |应为加密密钥定义非永久性到期日期。 密钥永久有效会导致潜在攻击者有更多时间来破解密钥。 建议的安全做法是为加密密钥设置到期日期。<br />（相关策略：[Key Vault 密钥应具有到期日期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0)） |高 |
|Key Vault 机密应具有到期日期 |应为机密定义非永久性到期日期。 机密永久有效会导致潜在攻击者有更多时间来破解密钥。 建议的安全做法是为机密设置到期日期。<br />（相关策略：[Key Vault 机密应具有到期日期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f98728c90-32c7-4049-8429-847dc0f4fe37)） |高 |
|密钥保管库应启用清除保护 |恶意删除密钥保管库可能会导致永久丢失数据。 你组织中的恶意内部人员可能会删除和清除密钥保管库。 清除保护通过强制实施软删除密钥保管库的强制保留期来保护你免受内部攻击。 你的组织内的任何人都无法在软删除保留期内清除你的密钥保管库。<br />（相关策略：[密钥保管库应启用清除保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0b60c0b2-2dc2-4e1c-b5c9-abbed971de53)） |中型 |
|密钥保管库应启用软删除 |在未启用软删除的情况下删除密钥保管库，将永久删除密钥保管库中存储的所有机密、密钥和证书。 意外删除密钥保管库可能会导致永久丢失数据。 软删除允许在可配置的保持期内恢复意外删除的密钥保管库。<br />（相关策略：[密钥保管库应启用软删除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d)） |高 |
|应在对订阅拥有所有者权限的帐户上启用 MFA |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。<br />（相关策略：[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faa633080-8b72-40c4-a2d7-d00c03e80bed)） |高 |
|应在对订阅拥有读取权限的帐户上启用 MFA |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。<br />（相关策略：[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fe3576e28-8b17-4677-84c3-db2990658d64)） |高 |
|应在对订阅拥有写入权限的帐户上启用 MFA |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。<br />（相关策略：[应在对订阅拥有写入权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f9297c21d-2ed6-4474-b48f-163f75654ce3)） |高 |
|应为 Key Vault 配置专用终结点 |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。<br />（相关策略：[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5f0bc445-3935-4915-9981-011aa2b46147)） |中型 |
|应使用服务主体（而不是管理证书）来保护你的订阅 |通过管理证书，任何使用它们进行身份验证的人员都可管理与它们关联的订阅。 为了更安全地管理订阅，建议将服务主体和资源管理器结合使用来限制证书泄露所造成的影响范围。 这也可以使资源管理自动进行。 <br />（相关策略：[应使用服务主体（而不是管理证书）来保护你的订阅](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6646a0bd-e110-40ca-bb97-84fcee63c414)） |中型 |
|应禁止存储帐户公共访问 |对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但可能会带来安全风险。 为了防止不希望的匿名访问导致数据泄露，Microsoft 建议禁止对存储帐户的公共访问，除非你的方案需要。<br />（相关策略：[应禁止存储帐户公共访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fmicrosoft.authorization%2fpolicyDefinitions%2f4fa4b6c0-31ca-4c0d-b10d-24b96f62a751)） |中型 |
|应该为你的订阅分配了多个所有者 |指定多个订阅所有者，以实现管理员访问权限冗余。<br />（相关策略：[应向订阅分配多个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f09024ccc-0c5f-475e-9457-b7c0d9ed487b)） |高 |
|存储在 Azure Key Vault 中的证书的有效期不得超过 12 个月 |请确保证书的有效期不超过 12 个月。<br />（相关策略：[证书应具有指定的最长有效期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0a075868-4c26-42ef-914c-5bc007359560)） |中型 |
|||
