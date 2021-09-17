---
title: 安全性概述
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的安全性（包括其与 SQL Server 中的不同之处）。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 08/23/2021
ms.openlocfilehash: 9326797e16190b3570ed6faca4d724bec432bc86
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767912"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL 数据库和 Azure SQL 托管实例安全功能概述
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文概述使用 [Azure SQL 数据库](sql-database-paas-overview.md)、[Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 保护应用程序数据层的基础知识。 所述的安全策略遵循如下图所示的分层深度防御方法，并从外向内移动：

![分层深度防御的关系图。 客户数据将包含在网络安全层、访问管理层以及威胁和信息保护层中。](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>网络安全性

Microsoft Azure SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 为云和企业应用程序提供关系数据库服务。 为了帮助保护客户数据，防火墙会阻止对服务器的网络访问，直到根据 IP 地址或 Azure 虚拟网络流量源显式授予访问权限。

### <a name="ip-firewall-rules"></a>IP 防火墙规则

IP 防火墙规则基于每个请求的起始 IP 地址授予对数据库的访问权限。 有关详细信息，请参阅 [Azure SQL 数据库和 Azure Synapse Analytics 防火墙规则概述](firewall-configure.md)。

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则

[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)将虚拟网络连接扩展到 Azure 主干网，并使 Azure SQL 数据库能够识别作为流量来源的虚拟网络子网。 若要允许流量到达 Azure SQL 数据库，请使用 SQL [服务标记](../../virtual-network/network-security-groups-overview.md)，以允许出站流量通过网络安全组。

[虚拟网络规则](vnet-service-endpoint-rule-overview.md)使 Azure SQL 数据库仅接受从虚拟网络中的所选子网发送的通信。

> [!NOTE]
> 使用防火墙规则控制访问权限不应用于“SQL 托管实例”。 有关所需网络配置的详细信息，请参阅[连接到托管实例](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>访问管理

> [!IMPORTANT]
> 管理 Azure 中的数据库和服务器由门户用户帐户的角色分配控制。 有关本文的详细信息，请参阅 [Azure 门户中 Azure 基于角色的访问控制](../../role-based-access-control/overview.md)。

### <a name="authentication"></a>身份验证

身份验证是证明用户所声明身份的过程。 Azure SQL 数据库和 SQL 托管实例支持两种类型的身份验证：

- **SQL 身份验证**：

    SQL 身份验证是指使用用户名和密码连接到 Azure SQL 数据库或 Azure SQL 托管实例时对用户进行的身份验证。 在创建服务器时，需要指定含用户名和密码的“服务器管理员”登录名。 借助这些凭据，“服务器管理员”可以使用数据库所有者的身份向服务器或实例上的任何数据库进行身份验证。 之后，服务器管理员可以创建额外的 SQL 登录和用户，以允许用户使用用户名和密码进行连接。

- **Azure Active Directory 身份验证**：

    Azure Active Directory 身份验证是使用 Azure Active Directory (Azure AD) 中的标识连接到 [Azure SQL 数据库](sql-database-paas-overview.md)、[Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 的一种机制。 使用 Azure AD 身份验证，管理员可在一个中心位置集中管理数据库用户以及其他 Azure 服务的标识和权限。 这包括最小化密码存储并启用集中式密码轮换策略。

     必须创建一个名为“Active Directory 管理员”的服务器管理员，以便在 SQL 数据库中使用 Azure AD 身份验证。 有关详细信息，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库](authentication-aad-overview.md)。 Azure AD 身份验证同时支持托管帐户和联合帐户。 联合帐户支持与 Azure AD 联合的客户域的 Windows 用户和组。

    其他可用的 Azure AD 身份验证选项包括[适用于 SQL Server Management Studio 的 Active Directory 通用身份验证](authentication-mfa-ssms-overview.md)连接，其中包括[多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)和[条件访问](conditional-access-configure.md)。

> [!IMPORTANT]
> 管理 Azure 中的数据库和服务器由门户用户帐户的角色分配控制。 有关本文的详细信息，请参阅 [Azure 门户中 Azure 基于角色的访问控制](../../role-based-access-control/overview.md)。 使用防火墙规则控制访问权限不应用于“SQL 托管实例”。 有关所需网络配置的详细信息，请参阅以下有关[连接到托管实例](../managed-instance/connect-application-instance.md)的文章。

## <a name="authorization"></a>授权

授权是指控制对数据库内资源和命令的访问。 为此，可以向 Azure SQL 数据库或 Azure SQL 托管实例中的数据库中的用户分配权限。 要以理想方式来管理权限，可以将用户帐户添加到[数据库角色](/sql/relational-databases/security/authentication-access/database-level-roles)并为这些角色分配数据库级别的权限。 或者，也可以向单个用户授予某些[对象级别的权限](/sql/relational-databases/security/permissions-database-engine)。 有关详细信息，请参阅[登录和用户](logins-create-manage.md)

最佳做法是根据需要创建自定义角色。 将用户添加到具有完成其作业功能所需的最低权限的角色中。 请勿直接将权限分配给用户。 服务器管理员帐户是内置的 db_owner 角色的成员，该角色具有广泛权限，只应将其授予部分具有管理职责的用户。 若要进一步限制用户可以执行的操作范围，可以使用 [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) 来指定被调用模块的执行上下文。 遵循这些最佳实践也是实现职责分离的基本步骤。

### <a name="row-level-security"></a>行级别安全性

行级别安全性使客户可以基于执行查询的用户的特性（例如，组成员身份或执行上下文）来控制对数据库表进行的访问。 行级别安全性也可用于实现基于自定义标签的安全概念。 有关详细信息，请参阅[行级别安全性](/sql/relational-databases/security/row-level-security)。

![此图显示了行级别安全性屏蔽了 SQL 数据库的各个行，以防用户通过客户端应用进行访问。](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>威胁防护

SQL 数据库和 SQL 托管实例通过提供审核和威胁检测功能来保护客户数据。

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor 日志和事件中心中的 SQL 审核

SQL 数据库和 SQL 托管实例审核可跟踪数据库活动，通过将数据库事件记录到客户所有的 Azure 存储帐户中的审核日志，帮助用户保持符合安全标准。 用户可以通过审核监视正在进行的数据库活动，以及分析和调查历史活动，以标识潜在威胁或可疑的滥用行为和安全违规。 有关详细信息，请参阅 [SQL 数据库审核入门](../../azure-sql/database/auditing-overview.md)。  

### <a name="advanced-threat-protection"></a>高级威胁防护

高级威胁防护通过对你的日志进行分析来检测异常行为和对数据库的潜在恶意访问或利用。 针对可疑活动（例如 SQL注入、潜在的数据渗透和暴力攻击）或访问模式中的异常情况创建警报，以捕获特权提升和违规的凭据使用。 可以从 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看警报，其中提供了可疑活动的详细信息，并给出了进一步调查建议以及缓解威胁的措施。 可以为每台服务器启用高级威胁防护，但需要额外付费。 有关详细信息，请参阅 [SQL 数据库高级威胁防护入门](threat-detection-configure.md)。

![此图显示了 SQL 威胁检测正在监视外部攻击者和恶意内部人员对 Web 应用的 SQL 数据库的访问。](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="transport-layer-security-encryption-in-transit"></a>传输层安全性（传输中加密）

SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 通过使用[传输层安全性 (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 加密动态数据来保护客户数据。

对于所有连接，SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 始终强制执行加密 (SSL/TLS)。 这样可以确保在客户端与服务器之间传输的所有数据经过加密，而不管连接字符串中的 **Encrypt** 或 **TrustServerCertificate** 设置如何。

作为最佳做法，建议在应用程序使用的连接字符串中指定加密的连接，而“不要”信任服务器证书。 这会强制应用程序验证服务器证书，从而防止应用程序容易受到中间人类型的攻击。

例如，在使用 ADO.NET 驱动程序时，这是通过 **Encrypt=True** 和 **TrustServerCertificate=False** 实现的。 如果是从 Azure 门户中获取连接字符串，则它将具有正确的设置。

> [!IMPORTANT]
> 请注意，某些非 Microsoft 驱动程序默认可能不使用 TLS，或者依赖于旧版 TLS (<1.2) 来正常运行。 在这种情况下，服务器仍允许连接到数据库。 但是，我们建议评估允许此类驱动程序和应用程序连接到 SQL 数据库所带来的安全风险，尤其是存储敏感数据时。
>
> 有关 TLS 和连接的更多信息，请参阅 [TLS 注意事项](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

### <a name="transparent-data-encryption-encryption-at-rest"></a>透明数据加密（静态加密）

[SQL 数据库、SQL 托管实例和 Azure Synapse Analytics 的透明数据加密 (TDE)](transparent-data-encryption-tde-overview.md) 进一步加强了安全性，帮助保护静态数据不受未经授权或脱机访问原始文件或备份的影响。 常见方案包括数据中心被盗或对硬件或媒体（如磁盘驱动器和备份磁带）的不安全处置。 TDE 使用 AES 加密算法加密整个数据库，无需应用程序开发人员对现有应用程序进行任何更改。

在 Azure 中，所有新创建的数据库都默认处于加密状态，且数据库加密密钥通过一个内置的服务器证书保护。  证书维护和轮换由服务管理，无需用户输入。 喜欢控制加密密钥的客户可以管理 [Azure Key Vault](../../key-vault/general/security-features.md) 中的密钥。

### <a name="key-management-with-azure-key-vault"></a>使用 Azure Key Vault 的密钥管理

[创建自己的密钥](transparent-data-encryption-byok-overview.md) (BYOK) 支持 [透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE)，允许客户使用  [Azure Key Vault](../../key-vault/general/security-features.md)（Azure 基于云的外部密钥管理系统）来获得密钥管理和轮换的所有权。 如果撤销了数据库对密钥保管库的访问权限，则无法解密数据库和将其读入内存。 Azure Key Vault 提供集中密钥管理平台，利用严格监控的硬件安全模块 (HSM)，并可在密钥与数据管理之间实现职责分离，以帮助满足安全合规性要求。

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted（使用中加密）

![此图显示了 Always Encrypted 功能的基础知识。 只有包含密钥的应用才能访问带锁的 SQL 数据库。](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 功能旨在保护特定数据库列中存储的敏感数据不被访问（如信用卡号或、国民身份证号或视需要而定的数据）。 这包括数据库管理员或其他特权用户，他们被授权访问数据库以执行管理任务，但不需要访问加密列中的特定数据。 数据始终处于加密状态，这意味着加密数据只在有权访问加密密钥的客户端应用程序需要处理数据时才解密。 加密密钥从不暴露给 SQL 数据库或 SQL 托管实例，而且可以存储在 [Windows 证书存储](always-encrypted-certificate-store-configure.md)或 [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) 中。

### <a name="dynamic-data-masking"></a>动态数据屏蔽

![此图显示了动态数据掩码。 商业应用将数据发送到 SQL 数据库，该数据库会对数据进行掩码，然后再将其发送回商业应用。](./media/security-overview/azure-database-ddm.png)

动态数据屏蔽通过对非特权用户屏蔽敏感数据来限制敏感数据的公开。 动态数据掩码可自动发现 Azure SQL 数据库和 SQL 托管实例中潜在的敏感数据，提供可行的建议来掩码这些字段，对应用程序层造成的影响可忽略不计。 它的工作原理是在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。 有关详细信息，请参阅 [SQL 数据库和 SQL 托管实例动态数据掩码入门](dynamic-data-masking-overview.md)。

## <a name="security-management"></a>安全管理

### <a name="vulnerability-assessment"></a>漏洞评估

[漏洞评估](sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪和帮助修正潜在的数据库漏洞，旨在主动提高整体数据库安全性。 漏洞评估 (VA) 是 Azure Defender for SQL 产品/服务（高级 SQL 安全功能的统一包）的一部分。 可通过中心 Azure Defender for SQL 门户访问和管理漏洞评估。

### <a name="data-discovery-and-classification"></a>数据发现和分类

数据发现和分类（当前为预览版）提供了内置于 Azure SQL 数据库和 SQL 托管实例的基本功能，可用于发现、分类和标记数据库中的敏感数据。 发现最敏感的数据（业务/财务、医疗保健、个人数据等）并进行分类，可在组织的信息保护方面发挥关键作用。 它可以充当基础结构，用于：

- 各种安全方案，如监视（审核）并在敏感数据存在异常访问时发出警报。
- 控制对包含高度敏感数据的数据库的访问并增强其安全性。
- 帮助满足数据隐私标准和法规符合性要求。

有关详细信息，请参阅[数据发现和分类入门](data-discovery-and-classification-overview.md)。

### <a name="compliance"></a>合规性

除了上述有助于应用程序符合各项安全要求的特性和功能以外，Azure SQL 数据库还定期参与审核，并已通过许多法规标准的认证。 有关详细信息，请参阅 [Microsoft Azure 信任中心](https://www.microsoft.com/trust-center/compliance/compliance-overview)，你可在这里找到 SQL 数据库符合性认证的最新列表。

## <a name="next-steps"></a>后续步骤

- 有关如何使用 SQL 数据库和 SQL 托管实例中的登录名、用户帐户、数据库角色和权限，请参阅[管理登录名和用户帐户](logins-create-manage.md)。
- 有关数据库审核的讨论，请参阅[审核](../../azure-sql/database/auditing-overview.md)。
- 有关威胁检测的讨论，请参阅[威胁检测](threat-detection-configure.md)。
