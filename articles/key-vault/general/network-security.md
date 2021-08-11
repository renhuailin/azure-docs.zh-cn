---
title: 配置 Azure 密钥保管库防火墙和虚拟网络 - Azure 密钥保管库
description: 了解密钥保管库网络设置
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3e9332706c3bcc9d4f4cabd20d10fa099271d69
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285330"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>配置 Azure Key Vault 防火墙和虚拟网络

本文档将详细介绍密钥保管库防火墙的不同配置。 若要按照有关如何配置这些设置的分步说明操作，请遵循[此处](how-to-azure-key-vault-network-security.md)的指南 

有关详细信息，请参阅[适用于 Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)。

## <a name="firewall-settings"></a>防火墙设置

本部分将介绍可用于配置 Azure Key Vault 防火墙的不同方式。

### <a name="key-vault-firewall-disabled-default"></a>禁用 Key Vault 防火墙（默认值）

默认情况下，当你创建新的密钥保管库时，Azure Key Vault 防火墙处于禁用状态。 所有应用程序和 Azure 服务都可以访问该密钥保管库并将请求发送到该密钥保管库。 请注意，此配置并不意味着任何用户都可以在你的密钥保管库上执行操作。 密钥保管库仍通过要求 Azure Active Directory 身份验证和访问策略权限来限制存储在密钥保管库中的机密、密钥和证书。 若要更详细地了解密钥保管库身份验证，请参阅[此处](./authentication.md)的密钥保管库身份验证基础知识文档。 有关详细信息，请参阅[访问防火墙保护下的 Azure 密钥保管库](./access-behind-firewall.md)。

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>启用 Key Vault 防火墙（仅限受信任的服务）

启用 Key Vault 防火墙时，系统将向你提供“允许受信任的 Microsoft 服务绕过此防火墙”的选项。 受信任的服务列表并不是全部的 Azure 服务。 例如，Azure DevOps 不在受信任的服务列表中。 这并不意味着未出现在受信任的服务列表中的服务不受信任或不安全。 受信任的服务列表中包含的服务符合这一条件：Microsoft 控制该服务上运行的所有代码。 由于用户可以在 Azure 服务（例如 Azure DevOps）中编写自定义代码，因此 Microsoft 不提供为该服务创建全面批准的选项。 此外，服务出现在受信任的服务列表中并不意味着所有方案都允许该服务。 

若要确定你尝试使用的服务是否在受信任的服务列表中，请参阅[此处](./overview-vnet-service-endpoints.md#trusted-services)的以下文档。
有关操作指南，请遵循此处有关[门户、Azure CLI 和 Powershell](how-to-azure-key-vault-network-security.md) 的说明执行操作

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>启用 Key Vault 防火墙（IPv4 地址和范围 - 静态 IP）

如果你想要授权特定服务通过 Key Vault 防火墙访问密钥保管库，可将其 IP 地址添加到密钥保管库防火墙允许列表中。 此配置最适合使用静态 IP 地址或已知范围的服务。 这种情况下，CIDR 范围数量限制为 1000。

若要允许某个 Azure 资源（例如 Web 应用或逻辑应用）的某个 IP 地址或范围，请执行以下步骤。

1. 登录到 Azure 门户
1. 选择资源（服务的特定实例）
1. 单击“设置”下的“属性”边栏选项卡
1. 查找“IP 地址”字段。
1. 复制此值或范围，并将其输入到密钥保管库防火墙允许列表中。

若要允许整个 Azure 服务通过 Key Vault 防火墙，请使用[此处](https://www.microsoft.com/download/details.aspx?id=41653)的 Azure 公开记录的数据中心 IP 地址列表。 在所需区域中找到与服务关联的 IP 地址，并使用上述步骤将这些 IP 地址添加到密钥保管库防火墙。

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>启用 Key Vault 防火墙（虚拟网络 - 动态 IP）

如果尝试允许 Azure 资源（如虚拟机）通过密钥保管库，则可能无法使用静态 IP 地址，并且你可能不希望允许 Azure 虚拟机的所有 IP 地址访问密钥保管库。

在这种情况下，应在虚拟网络中创建资源，然后允许来自特定虚拟网络和子网的流量访问密钥保管库。 为此，请执行以下步骤。

1. 登录到 Azure 门户
1. 选择要配置的密钥保管库
1. 选择“网络”边栏选项卡
1. 选择“+ 添加现有虚拟网络”
1. 选择要允许通过密钥保管库防火墙的虚拟网络和子网。

### <a name="key-vault-firewall-enabled-private-link"></a>启用 Key Vault 防火墙（专用链接）

若要了解如何在密钥保管库上配置专用链接连接，请参阅[此处](./private-link-service.md)的文档。

> [!IMPORTANT]
> 防火墙规则生效后，只在用户请求来自允许的虚拟网络或 IPv4 地址范围时，才能执行 Key Vault [数据平面](security-features.md#privileged-access)操作。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

> [!NOTE]
> 注意以下配置限制：
> * 最多允许 200 条虚拟网络规则和 1000 条 IPv4 规则。 
> * IP 网络规则仅适用于公共 IP 地址。 IP 规则不允许为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。 专用网络包括以 **10.** 、**172.16-31** 和 **192.168.** 开头的地址。 
> * 目前仅支持 IPv4 地址。

## <a name="references"></a>参考
* ARM 模板参考：[Azure Key Vault ARM 模板参考](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlets：[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
* [Azure Key Vault 安全性概述](security-features.md)