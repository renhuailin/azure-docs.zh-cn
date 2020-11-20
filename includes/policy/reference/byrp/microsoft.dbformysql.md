---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2597d3cb40094e94226035418009f989fe0ba9d1
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985650"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为 MySQL 服务器启用“创建自己的密钥”数据保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |使用客户管理的密钥对 Azure Database for MySQL 数据库服务中的静态数据进行加密，从而在密钥和数据管理中实现职责分离。 配置客户管理的密钥时，可以使用该密钥保护和控制对加密数据的密钥的访问。 你可以完全控制并负责关键生命周期，包括轮换和管理。 有时出于合规性目的，需要使用客户管理的密钥。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[应为 MySQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 此配置强制始终启用 SSL 以访问数据库服务器。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |通过 Azure Database for MySQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以在发生区域故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for MySQL 服务器启用基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |为 Azure Database for MySQL 服务器启用基础结构加密，以便增强数据安全。 启用基础结构加密后，将使用符合 FIPS 140-2 的 Microsoft 托管密钥对静态数据进行两次加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[MySQL 服务器应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |基于虚拟网络的防火墙规则用于支持从特定子网到 Azure Database for MySQL 的流量，同时确保流量停留在 Azure 边界内。 此策略提供了一种方法来审核 Azure Database for MySQL 是否正在使用虚拟网络服务终结点。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 灵活服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9299215-ae47-4f50-9c54-8a392f68a052) |禁用公用网络访问属性可确保只能从专用终结点访问 Azure Database for MySQL 灵活服务器，从而提高安全性。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_FlexibleServers_DisablePublicNetworkAccess_Audit.json) |
|[应为 MySQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |禁用公用网络访问属性可确保只能从专用终结点访问 Azure Database for MySQL，从而提高安全性。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
