---
title: Azure Key Vault 的虚拟网络服务终结点
description: 了解如何使用 Azure Key Vault 的虚拟网络服务终结点将访问限制为仅允许通过指定的虚拟网络（包括使用方案）进行访问。
services: key-vault
author: mbaldwin
ms.author: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 4ee06e1eed38a1fbb524695cb4835534604f01a7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461774"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault 的虚拟网络服务终结点

通过 Azure Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络。 此外，还可通过这些终结点将访问限制为一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到 Key Vault 的用户都无法访问这些资源。

此限制有一个重要的例外情况。 若用户已选择允许受信任的 Microsoft 服务访问，则会允许来自这些服务的连接通过防火墙。 这些服务包括 Office 365 Exchange Online、Office 365 SharePoint Online、Azure 计算、Azure 资源管理器和 Azure 备份等。 此类用户仍需提供有效的 Azure Active Directory 令牌，并且必须具有执行所请求的操作的权限（配置为访问策略）。 有关详细信息，请参阅[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="usage-scenarios"></a>使用方案

可以将 [Key Vault 防火墙和虚拟网络](network-security.md)配置为默认拒绝访问来自所有网络的流量（包括 Internet 流量）。 可以向来自特定 Azure 虚拟网络和公共 Internet IP 地址范围的流量授予访问权限，为应用程序构建安全的网络边界。

> [!NOTE]
> Key Vault 防火墙和虚拟网络规则仅适用于 Key Vault [数据平面](security-features.md#privileged-access)。 Key Vault 控制平面操作（例如创建、删除和修改操作，设置访问策略，设置防火墙和虚拟网络规则，以及通过 ARM 模板部署机密或密钥）不受防火墙和虚拟网络规则的影响。

下面是此服务终结点的一些用法示例：

* 使用 Key Vault 存储加密密钥、应用程序机密和证书，并希望阻止从公共 Internet 访问 Key Vault。
* 你希望限制访问 Key Vault，以便只有你的应用程序或指定的少部分主机才能连接到 Key Vault。
* 你有一个在 Azure 虚拟网络中运行的应用程序，并且此虚拟网络限制了所有的入站和出站流量。 应用程序仍需连接到 Key Vault，以获取机密或证书，或者使用加密密钥。

## <a name="trusted-services"></a>受信服务

以下是允许访问 Key Vault 的受信服务列表（前提是启用了“允许受信任的服务”选项）。

|受信服务|支持的使用方案|
| --- | --- |
|Azure 虚拟机部署服务|[将证书从客户托管的 Key Vault 部署到 VM](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault)。|
|Azure 资源管理器模板部署服务|[在部署期间传递安全值](../../azure-resource-manager/templates/key-vault-parameter.md)。|
|Azure 磁盘加密卷加密服务|允许在虚拟机部署期间访问 BitLocker 密钥 (Windows VM) 或 DM 密码 (Linux VM) 和密钥加密密钥。 这将启用 [Azure 磁盘加密](../../security/fundamentals/encryption-overview.md)。|
|Azure 备份|允许使用 [Azure 备份](../../backup/backup-overview.md)在 Azure 虚拟机备份期间备份和还原相关密钥和机密。|
|Exchange Online 和 SharePoint Online|允许使用[客户密钥](/microsoft-365/compliance/customer-key-overview)访问 Azure 存储服务加密的客户密钥。|
|Azure 信息保护|允许访问 [Azure 信息保护](/azure/information-protection/what-is-information-protection)的租户密钥。|
|Azure 应用服务|[通过 Key Vault 部署 Azure Web 应用证书](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)。|
|Azure SQL 数据库|[使用 Azure SQL 数据库和 Azure Synapse Analytics 的“创建自己的密钥”支持进行透明数据加密](../../azure-sql/database/transparent-data-encryption-byok-overview.md)。|
|Azure 存储|[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](../../storage/common/customer-managed-keys-configure-key-vault.md)。|
|Azure Data Lake Store|[在 Azure Data Lake Store 中使用客户托管密钥进行数据加密](../../data-lake-store/data-lake-store-encryption.md)。|
|Azure Synapse Analytics|[在 Azure Key Vault 中使用客户管理的密钥对数据进行加密](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[基于 Apache Spark 的快速、简单、协作分析服务](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API 管理|[使用 MSI 从 Key Vault 部署自定义域证书](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure 数据工厂|[从数据工厂提取 Key Vault 中的数据存储凭据](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure 事件中心|[允许访问客户管理的密钥方案的密钥保管库](../../event-hubs/configure-customer-managed-key.md)|
|Azure 服务总线|[允许访问客户管理的密钥方案的密钥保管库](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure 导入/导出| [将 Azure Key Vault 中的客户管理的密钥用于导入/导出服务](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure 容器注册表|[使用客户管理的密钥进行注册表加密](../../container-registry/container-registry-customer-managed-keys.md)
|Azure 应用程序网关 |[对启用 HTTPS 的侦听器使用 Key Vault 证书](../../application-gateway/key-vault-certs.md)
|Azure Front Door|[使用适用于 HTTPS 的 Key Vault 证书](../../frontdoor/front-door-custom-domain-https.md#prepare-your-azure-key-vault-account-and-certificate)

> [!NOTE]
> 必须设置相关 Key Vault 访问策略，才能允许相应的服务访问 Key Vault。

## <a name="next-steps"></a>后续步骤

- 有关分步说明，请参阅[配置 Azure Key Vault 防火墙和虚拟网络](network-security.md)
- 请参阅 [Azure Key Vault 安全性概述](security-features.md)
