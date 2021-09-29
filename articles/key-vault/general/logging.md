---
title: Azure Key Vault 日志记录 | Microsoft Docs
description: 了解如何通过启用 Azure Key Vault 的日志记录来监视对密钥保管库的访问权限，以便在提供的 Azure 存储帐户中保存信息。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 11df80fc8c418c76750f4fedb8a4116999800ecf
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058503"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 日志记录

在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 为此，可以启用 Azure Key Vault 日志记录，以便在提供的 Azure 存储帐户中保存信息。 有关设置此操作的分步指南，请参阅[如何启用 Key Vault 日志记录](howto-logging.md)。

最多在执行 Key Vault 操作 10 分钟后，就能访问其日志记录信息。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请在存储帐户中使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

有关 Key Vault的概述信息，请参阅[什么是 Azure Key Vault？](overview.md)。 有关 Key Vault 可用位置的信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/key-vault/)。 有关[将 Azure Monitor 用于 Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) 的信息。

## <a name="interpret-your-key-vault-logs"></a>解释 Key Vault 日志

启用日志记录时，系统会为你指定的存储帐户自动创建一个名为“insights-logs-auditevent”的新容器。 可以使用此同一个存储帐户来收集多个 Key Vault 的日志。

每个 Blob 存储为文本，并格式化为 JSON Blob。 让我们看一个示例日志项。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出了字段的名称和描述：

| 字段名称 | 说明 |
| --- | --- |
| **time** |日期和时间 (UTC)。 |
| **resourceId** |Azure 资源管理器资源 ID。 对于密钥保管库日志而言，这始终是密钥保管库资源 ID。 |
| **operationName** |下一份表格中所述操作的名称。 |
| **operationVersion** |客户端请求的 REST API 版本。 |
| **category** |结果的类型。 对于 Key Vault 日志而言，`AuditEvent` 是唯一可用值。 |
| **resultType** |REST API 请求的结果。 |
| **resultSignature** |HTTP 状态。 |
| **resultDescription** |有关结果的其他描述（如果有）。 |
| **durationMs** |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| **callerIpAddress** |发出请求的客户端的 IP 地址。 |
| **correlationId** |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端 (Key Vault) 日志相关联。 |
| **identity** |在 REST API 请求中提供的令牌中的标识。 与通过 Azure PowerShell cmdlet 发出请求一样，这通常是“用户”、“服务主体”，或者“用户+应用 ID”的组合。 |
| **properties** |此字段根据操作 (**operationName**) 包含不同的信息。 在大多数情况下，此字段包含客户端信息（客户端传递的用户代理字符串）、具体 REST API 请求 URI 和 HTTP 状态代码。 此外，在根据请求（例如，KeyCreate 或 VaultGet）返回对象时，此字段还将包含密钥 URI（`id` 形式）、保管库 URI 或机密 URI。 |

**operationName** 字段值采用 *ObjectVerb* 格式。 例如：

* 所有 Key Vault 操作采用 `Vault<action>` 格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有密钥操作采用 `Key<action>` 格式，例如 `KeySign` 和 `KeyList`。
* 所有机密操作采用 `Secret<action>` 格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出了 **operationName** 值和对应的 REST API 命令：

### <a name="operation-names-table"></a>操作名称表

# <a name="vault"></a>[保管库](#tab/Vault)

| operationName | REST API 命令 |
| --- | --- |
| **身份验证** |通过 Azure Active Directory 终结点进行身份验证 |
| **VaultGet** |[获取有关密钥保管库的信息](/rest/api/keyvault/vaults) |
| **VaultPut** |[创建或更新密钥保管库](/rest/api/keyvault/vaults) |
| **VaultDelete** |[删除密钥保管库](/rest/api/keyvault/vaults) |
| **VaultPatch** |[更新密钥保管库](/rest/api/keyvault/vaults) |
| **VaultList** |[列出资源组中的所有密钥保管库](/rest/api/keyvault/vaults) |
| **VaultPurge** |[清除已删除的保管库](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |恢复已删除的保管库|
| **VaultGetDeleted** |[获取已删除的保管库](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[列出已删除的保管库](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | 保管库访问策略更改事件已发布 |

# <a name="keys"></a>[“键”](#tab/Keys)

| operationName | REST API 命令 |
| --- | --- |
| **KeyCreate** |[创建密钥](/rest/api/keyvault/createkey) |
| **KeyGet** |[获取有关密钥的信息](/rest/api/keyvault/getkey) |
| **KeyImport** |[将密钥导入保管库](/rest/api/keyvault/vaults) |
| **KeyDelete** |[删除密钥](/rest/api/keyvault/deletekey) |
| **KeySign** |[使用密钥签名](/rest/api/keyvault/sign) |
| **KeyVerify** |[使用密钥验证](/rest/api/keyvault/vaults) |
| **KeyWrap** |[包装密钥](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[解包密钥](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[使用密钥加密](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[使用密钥解密](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[更新密钥](/rest/api/keyvault/updatekey) |
| **KeyList** |[列出保管库中的密钥](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[列出密钥的版本](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[清除密钥](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[备份密钥](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[还原密钥](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[恢复密钥](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[获取已删除的密钥](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[列出保管库中已删除的密钥](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |密钥即将过期事件已发布 |
| **KeyExpiredEventGridNotification** |密钥已过期事件已发布 |

# <a name="secrets"></a>[机密](#tab/Secrets)

| operationName | REST API 命令 |
| --- | --- |
| **SecretSet** |[创建机密](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[获取机密](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[更新机密](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[删除机密](/rest/api/keyvault/deletesecret) |
| **SecretList** |[列出保管库中的机密](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[列出机密的版本](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[清除机密](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[备份机密](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[还原机密](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[恢复机密](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[获取已删除的机密](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[列出保管库中已删除的机密](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |机密即将过期事件已发布 |
| **SecretExpiredEventGridNotification** |机密已过期事件已发布 |

# <a name="certificates"></a>[证书](#tab/Cerificates)

| operationName | REST API 命令 |
| --- | --- |
| **CertificateGet** |[获取有关证书的信息](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[创建证书](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[将证书导入保管库](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[更新证书](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[列出保管库中的证书](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[列出证书的版本](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[删除证书](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[清除证书](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[备份证书](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[还原证书](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[恢复证书](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[获取已删除的证书](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[列出保管库中已删除的证书](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[获取证书策略](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[更新证书策略](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[创建证书策略](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[获取证书联系人](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[设置证书联系人](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[删除证书联系人](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[获取证书颁发者](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[设置证书颁发者](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[更新证书颁发者](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[删除证书颁发者](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[列出证书颁发者](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |注册证书 |
| **CertificateRenew** |续订证书 |
| **CertificatePendingGet** |检索挂起的证书 |
| **CertificatePendingMerge** | 证书合并挂起 |
| **CertificatePendingUpdate** | 证书更新挂起 |
| **CertificatePendingDelete** |删除挂起的证书 |
| **CertificateNearExpiryEventGridNotification** |证书即将过期事件已发布 |
| **CertificateExpiredEventGridNotification** |证书已过期事件已发布 |

---

## <a name="use-azure-monitor-logs"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看 Key Vault `AuditEvent` 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。 

有关详细信息，包括如何进行设置，请参阅 [Azure Monitor 中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a>后续步骤

- [如何启用 Key Vault 日志记录](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- 有关在 .NET Web 应用程序中使用 Azure Key Vault 的教程，请参阅[从 Web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。
- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](developers-guide.md)。
- 有关 Azure Key Vault 的 Azure PowerShell 1.0 cmdlet 列表，请参阅 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/#key_vault)。
