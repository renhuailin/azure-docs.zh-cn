---
title: 使用 Azure Key Vault 证书进行 TLS 终止
description: 了解如何将 Azure 应用程序网关与 Key Vault 集成，以便存储附加到支持 HTTPS 的侦听器的服务器证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 15119b3196735e2358b76c1a804ca25f67b05efa
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809934"
---
# <a name="tls-termination-with-key-vault-certificates"></a>使用 Key Vault 证书进行 TLS 终止

[Azure Key Vault](../key-vault/general/overview.md) 是平台托管的机密存储，可以用来保证机密、密钥和 TLS/SSL 证书的安全。 Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 此支持仅限 v2 SKU 版应用程序网关。

应用程序网关为 TLS 终止提供两种模型：

- 提供附加到侦听器的 TLS/SSL 证书。 此模型是将 TLS/SSL 证书传递到应用程序网关进行 TLS 终止的传统方式。
- 在创建启用 HTTPS 的侦听器时，提供对现有密钥保管库证书或机密的引用。

应用程序网关与 Key Vault 集成具有许多优势，其中包括：

- 更高的安全性，因为 TLS/SSL 证书不直接由应用程序开发团队处理。 集成允许独立的安全团队执行以下操作：
  * 设置应用程序网关。
  * 控制应用程序网关生命周期。
  * 授权选定的应用程序网关访问存储在密钥保管库中的证书。
- 支持将现有证书导入密钥保管库中。 或者使用 Key Vault API 与任何受信任的 Key Vault 合作伙伴一起创建并管理新证书。
- 支持自动续订存储在密钥保管库中的证书。

## <a name="supported-certificates"></a>支持的证书

应用程序网关目前仅支持经软件验证的证书。 不支持硬件安全模块 (HSM) 验证的证书。 

在应用程序网关在配置为使用 Key Vault 证书以后，其实例会从 Key Vault 检索证书并将其安装到本地进行 TLS 终止。 实例按 4 小时的时间间隔轮询密钥保管库，以便检索证书的续订版本（如果存在）。 如果发现了更新的证书，则目前与 HTTPS 侦听器关联的 TLS/SSL 证书会自动轮换。 

应用程序网关在密钥保管库中使用机密标识符引用证书。 对于 Azure PowerShell、Azure CLI 或 Azure 资源管理器，我们强烈建议使用未指定版本的机密标识符。 这样，如果密钥保管库中有更新的版本可用，应用程序网关将自动轮换证书。 没有版本的机密 URI 的一个示例是 `https://myvault.vault.azure.net/secrets/mysecret/`。

Azure 门户仅支持密钥保管库证书，而不支持机密。 应用程序网关仍支持从密钥保管库引用机密，但只能通过非门户资源，如 PowerShell、Azure CLI、API 和 Azure 资源管理器模板（ARM 模板）。

> [!WARNING]
> Azure 应用程序网关当前仅支持与应用程序网关资源位于同一订阅中的密钥保管库帐户。 选择不包含你的应用程序网关的订阅中的密钥保管库会导致失败。

## <a name="certificate-settings-in-key-vault"></a>密钥保管库中的证书设置

对于 TLS 终止，应用程序网关支持个人信息 Exchange (PFX) 格式的证书。 然后导入现有的证书，或者在密钥保管库中创建新证书。 若要避免任何失败，请确保在密钥保管库中将证书的状态设置为“已启用”。

## <a name="how-integration-works"></a>集成工作原理

应用程序网关与密钥保管库集成是一个三步配置过程：

![将应用程序网关与密钥保管库集成的三步骤演示图。](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

可以创建用户分配的托管标识或重用现有的标识。 应用程序网关使用托管标识，以代表你的身份从密钥保管库检索证书。 有关详细信息，请参阅[使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

这一步在 Azure Active Directory 租户中创建新标识。 此标识受那个用来创建标识的订阅的信任。

### <a name="configure-your-key-vault"></a>配置密钥保管库

定义用于将用户分配的托管标识与密钥保管库一起使用时的访问策略：
    
1. 在 Azure 门户中，转到“密钥保管库”。
1. 打开“访问策略”窗格。
1. 如果使用的是权限模型“保管库访问策略”：选择“+ 添加访问策略”，针对选择“机密权限”选择“获取”，然后针对“选择主体”选择由用户分配的托管标识。     再选择“保存”。
   
   如果正在使用权限模型“Azure 基于角色的访问控制”：将用户分配的托管标识的角色分配添加到角色“Key Vault 机密用户”的 Azure 密钥保管库。 

从 2021 年 3 月 15 日起，Key Vault 通过利用用户托管标识在 Azure Key Vault 中进行身份验证，从而将应用程序网关识别为受信任的服务。  使用服务终结点并为密钥保管库的防火墙启用“受信任的服务”选项，可以在 Azure 中构建安全的网络边界。 你可以拒绝访问从所有网络到密钥保管库的流量（包括 Internet 流量），但仍可让密钥保管库访问你订阅的应用程序网关资源。

使用受限制的密钥保管库时，请使用以下步骤将应用程序网关配置为使用防火墙和虚拟网络： 

1. 在 Azure 门户中的密钥保管库中，选择“网络”。
1. 在“防火墙和虚拟网络”选项卡上，选择“专用终结点和选定网络” 。
1. 对于“虚拟网络”，请选择“ + 添加现有虚拟网络”，然后为应用程序网关实例添加虚拟网络和子网。  在此过程中，还可以通过选中 `Microsoft.KeyVault` 服务终结点复选框来对其进行配置。
1. 选择“是”以允许受信任的服务绕过密钥保管库的防火墙。
  
![显示用于将应用程序网关配置为使用防火墙和虚拟网络的选项的屏幕截图。](media/key-vault-certs/key-vault-firewall.png)

如果通过 ARM 模板来部署应用程序网关实例（不管是使用 Azure CLI 还是使用 PowerShell），或通过从 Azure 门户部署的 Azure 应用程序来执行此操作，则 SSL 证书将以 Base64 编码的 PFX 文件形式存储在密钥保管库中。 必须完成[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/templates/key-vault-parameter.md)中的步骤。 

将 `enabledForTemplateDeployment` 设置为 `true` 尤其重要。 证书可能包含也可能没有密码。 对于包含密码的证书，以下示例显示了适用于应用程序网关的 ARM 模板配置的 `properties` 中的 `sslCertificates` 条目的可能配置。 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

将从密钥保管库中查找 `appGatewaySSLCertificateData` 和 `appGatewaySSLCertificatePassword` 的值，如[通过动态 ID 引用机密](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)中所述。 请遵循 `parameters('secretName')` 中的后向引用，了解查找方法。 如果证书无密码，请省略 `password` 条目。

### <a name="configure-application-gateway"></a>配置应用程序网关

创建用户分配的托管标识并配置密钥保管库之后，可以通过身份和访问管理 (IAM) 为应用程序网关实例分配托管标识。 对于 PowerShell，请参阅 [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)。

## <a name="investigating-and-resolving-key-vault-errors"></a>调查和解决密钥保管库错误

Azure 应用程序网关不会每隔 4 小时对密钥保管库上的续订证书版本进行轮询。 还会记录任何错误，并与 Azure 顾问集成，以将任何错误配置作为建议进行呈现。 建议中包含有关问题的详细信息以及关联的密钥保管库资源。 可以使用此信息以及[故障排除指南](../application-gateway/application-gateway-key-vault-common-errors.md)来快速解决此类配置错误。 

我们强烈建议在检测到问题时[配置 Advisor 警报](../advisor/advisor-alerts-portal.md)以保持最新。 若要针对这种特定情况设置警报，请使用 **解决应用程序网关的 Azure 密钥保管库问题** 作为建议类型。 

## <a name="next-steps"></a>后续步骤

[通过 Azure PowerShell 配置使用 Key Vault 证书的 TLS 终止](configure-keyvault-ps.md)
