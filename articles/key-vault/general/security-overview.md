---
title: Azure Key Vault 安全性概述
description: Azure Key Vault 的安全功能和最佳做法的概述。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071557"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 安全性

使用 Azure Key Vault 可以保护云中的加密密钥和机密，例如证书、连接字符串和密码。 存储敏感数据和关键业务数据时，需要采取措施来最大限度地提高保管库及其存储的数据的安全性。

本文概述了 Azure Key Vault 的安全功能和最佳做法。 

> [!NOTE]
> 有关 Azure Key Vault 安全建议的完整列表，请参阅 [Azure Key Vault 的安全基线](security-baseline.md)。

## <a name="network-security"></a>网络安全性

可以通过指定哪些 IP 地址有权访问来减少保管库的曝光。 通过 Azure Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络。 此外，还可通过这些终结点将访问限制为一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到 Key Vault 的用户都无法访问这些资源。  有关完整的详细信息，请参阅[适用于 Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)。

防火墙规则生效后，仅当用户请求来自允许的虚拟网络或 IPv4 地址范围时，才能读取 Key Vault 中的数据。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。  有关实施步骤，请参阅[配置 Azure Key Vault 防火墙和虚拟网络](network-security.md)。

使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure Key Vault 和 Azure 托管的客户服务/合作伙伴服务。 Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。  有关实施步骤，请参阅[将 Key Vault 与 Azure 专用链接集成](private-link-service.md)

## <a name="tls-and-https"></a>TLS 和 HTTPS

- Key Vault 前端（数据平面）是多租户服务器。 这意味着不同客户的密钥保管库可以共享同一公共 IP 地址。 为实现隔离，每个 HTTP 请求均独立于其他请求进行身份验证和授权。
- 可以标识较旧版本的 TLS 来报告漏洞，但由于公共 IP 地址是共享的，因此密钥保管库服务团队无法在传输级别为单个密钥保管库禁用较旧版本的 TLS。
- HTTPS 协议允许客户端参与 TLS 协商。 客户端可以强制实施最新版的 TLS，并且当客户端执行此操作时，整个连接将采用相应级别的保护。 Key Vault 仍支持较旧版的 TLS，但这不会影响使用较新版 TLS 的连接的安全性。
- 尽管 TLS 协议中存在已知漏洞，但在已知的攻击中，当攻击者使用存在漏洞的 TLS 版本启动某个连接时，尚无法通过恶意代理从密钥保管库中提取任何信息。 攻击者仍需要进行身份验证和授权，并且只要有权限的客户端始终连接最新版 TLS，凭据就不会通过旧版 TLS 的漏洞泄漏。

## <a name="identity-management"></a>身份管理

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 尝试管理或检索保管库内容的任何人都必须通过 Azure AD 进行身份验证。 在这两种情况下，应用程序都可以通过三种方式来访问 Key Vault：

- 仅应用程序：该应用程序表示服务主体或托管标识。 对于需要定期从密钥保管库中访问证书、密钥或机密的应用程序而言，此标识是最常见的方案。 为了让这种方案起作用，必须在访问策略中指定应用程序的 `objectId`，并且不能指定 `applicationId`，或者它必须为 `null`。
- 仅用户：用户从租户中注册的任何应用程序访问密钥保管库。 此类访问的示例包括 Azure PowerShell 和 Azure 门户。 这了让这种该方案起作用，必须在访问策略中指定用户的 `objectId`，并且不能指定 `applicationId`，或者它必须为 `null`。
- 应用程序和用户（有时称为“复合标识”）：用户需要从特定应用程序访问密钥保管库，并且该应用程序必须使用代理身份验证 (OBO) 流来模拟用户。 要使此方案起作用，必须在访问策略中指定 `applicationId` 和 `objectId`。 `applicationId` 标识所需的应用程序，`objectId` 标识用户。 目前，此选项不可用于数据平面 Azure RBAC（预览版）。

在所有类型的访问中，应用程序都使用 Azure AD 进行身份验证。 应用程序根据应用程序类型使用任何[支持的身份验证方法](../../active-directory/develop/authentication-vs-authorization.md)。 应用程序通过获取平面中资源的令牌来授予访问权限。 资源是管理平面或数据平面中基于 Azure 环境的终结点。 应用程序使用令牌并向密钥保管库发送 REST API 请求。 若要了解详细信息，请查看[整个身份验证流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

有关完整的详细信息，请参阅 [Key Vault 身份验证基础知识](authentication-fundamentals.md)

## <a name="privileged-access"></a>特权访问

授权可确定调用方能够执行的操作。 Key Vault 中的授权使用[基于 Azure 角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 和 Azure Key Vault 访问策略的组合。

可以通过两个接口或平面访问保管库。 这些平面为管理平面和数据平面。

- 管理平面是管理 Key Vault 本身的地方，也是用于创建和删除保管库的接口。 还可以读取密钥保管库属性并管理访问策略。
- 数据平面支持处理密钥保管库中存储的数据。 可以添加、删除和修改密钥、机密及证书。

应用程序通过终结点访问平面。 两个平面的访问控制独立运行。 若要授权应用程序使用密钥保管库中的密钥，可以使用 Key Vault 访问策略或 Azure RBAC（预览版）授予数据平面访问权限。 若要授予用户对 Key Vault 属性和标记的读取访问权限，但不授予对数据（密钥、机密或证书）的访问权限，请使用 Azure RBAC 来授予管理平面访问权限。

下表显示了用于管理平面和数据平面的终结点。

| 访问&nbsp;平面 | 访问终结点 | 操作 | 访问&nbsp;控制机制 |
| --- | --- | --- | --- |
| 管理平面 | **全球：**<br> management.azure.com:443<br><br> **Azure 中国世纪互联：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 | 创建、读取、更新和删除密钥保管库<br><br>设置密钥保管库访问策略<br><br>设置密钥保管库标记 | Azure RBAC |
| 数据平面 | **全球：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 中国世纪互联：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 密钥：加密、解密、包装密钥、解包密钥、签名、验证、获取、列出、创建、更新、导入、删除、恢复、备份、还原、清除<br><br> 证书：管理联系人、获取颁发者、列出颁发者、设置颁发者、删除颁发者、管理颁发者、获取、列出、创建、导入、更新、删除、恢复、备份、还原、清除<br><br>  机密：获取、列出、设置、删除、恢复、备份、还原、清除 | Key Vault 访问策略或 Azure RBAC（预览版）|

### <a name="managing-administrative-access-to-key-vault"></a>管理对 Key Vault 的管理访问权限

在资源组中创建密钥保管库时，使用 Azure AD 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可以通过分配适当的 Azure 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 Azure 角色：

- **订阅**：在订阅级别分配的 Azure 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 Azure 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 Azure 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [Azure RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。

### <a name="controlling-access-to-key-vault-data"></a>控制对 Key Vault 数据的访问

Key Vault 访问策略单独授予对密钥、机密或证书的权限。 可以仅授予用户对密钥的访问权限，而不授予对机密的访问权限。 在保管库级别管理密钥、机密或证书的访问权限。

> [!IMPORTANT]
> 密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。

可以使用 [Azure 门户](assign-access-policy-portal.md)、[Azure CLI](assign-access-policy-cli.md)、[Azure PowerShell](assign-access-policy-powershell.md) 或[密钥保管库管理 REST API](/rest/api/keyvault/) 为密钥保管库设置访问策略。

可以通过使用[适用于 Azure 密钥保管库的虚拟网络服务终结点](overview-vnet-service-endpoints.md)来限制数据平面访问权限）。 可以配置[防火墙和虚拟网络规则](network-security.md)以提供额外的安全层。

## <a name="logging-and-monitoring"></a>日志记录和监视

Key Vault 日志记录会保存保管库中所执行活动的相关信息。 有关完整详细信息，请参阅 [Key Vault 日志记录](logging.md)。

你可以将 Key Vault 与事件网格集成，以便在密钥保管库中存储的密钥、证书或机密的状态发生更改时收到通知。 有关详细信息，请参阅 [通过 Azure 事件网格监视 Key Vault](event-grid-overview.md)

还必须监视密钥保管库的运行状况，以确保服务按预期运行。 若要了解如何执行此操作，请参阅 [Azure Key Vault 的监视和警报](alert.md)。

## <a name="backup-and-recovery"></a>备份和恢复

Azure Key Vault 软删除和清除保护允许你恢复已删除的保管库和保管库对象。 有关完整的详细信息，请参阅 [Azure Key Vault 软删除概述](soft-delete-overview.md)。

在保管库中更新/删除/创建对象时，你还应定期备份保管库。  

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全基线](security-baseline.md)
- [Azure Key Vault 最佳做法](security-baseline.md)
- [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
- [Azure RBAC：内置角色](../../role-based-access-control/built-in-roles.md)
