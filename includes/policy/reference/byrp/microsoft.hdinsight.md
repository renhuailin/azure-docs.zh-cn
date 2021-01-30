---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 88945738b33cb6d1d7760eec1015a006132d3c3f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180438"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight 群集应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |使用客户管理的密钥 (CMK) 管理 Azure HDInsight 群集的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足监管合规标准，通常需要使用 CMK。 CMK 允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 访问 [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk)，了解有关 CMK 加密的详细信息。  |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight 群集应在主机上使用加密来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |在主机上启用加密可帮助保护数据，从而满足组织的安全性和符合性承诺。 启用主机加密时，存储在 VM 主机上的数据将静态加密，且已加密的数据将流向存储服务。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight 群集应使用传输中的加密来加密 Azure HDInsight 群集节点之间的通信](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |在 Azure HDInsight 群集节点之间传输的过程中，可能会篡改数据。 在传输过程中启用加密将解决在此传输过程中被滥用和篡改的问题。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
