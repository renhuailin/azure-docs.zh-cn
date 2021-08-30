---
title: 充当事件网格源的 Azure Key Vault
description: 介绍针对 Azure 事件网格中的 Azure Key Vault 事件提供的属性和架构
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 31a5e77cc238dcfc774ebc851e864c916206fd9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748468"
---
# <a name="azure-key-vault-as-event-grid-source"></a>充当事件网格源的 Azure Key Vault

本文提供了 [Azure Key Vault](../key-vault/index.yml) 中事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。


## <a name="available-event-types"></a>可用事件类型

Azure Key Vault 帐户生成以下事件类型：

| 事件全名 | 事件显示名称 | 说明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | 创建的证书新版本 | 创建新证书或新证书版本时触发。 |
| Microsoft.KeyVault.CertificateNearExpiry | 证书即将过期 | 当前版本的证书即将过期时触发。 （此事件在证书到期日期前 30 天触发。） |
| Microsoft.KeyVault.CertificateExpired | 证书已过期 | 当前版本的证书过期时触发。 |
| Microsoft.KeyVault.KeyNewVersionCreated | 创建的密钥新版本 | 创建新密钥或新密钥版本时触发。 |
| Microsoft.KeyVault.KeyNearExpiry | 密钥即将过期 | 当前版本的密钥即将过期时触发。 （此事件在证书到期日期前 30 天触发。） |
| Microsoft.KeyVault.KeyExpired | 密钥已过期 | 当前版本的密钥过期时触发。 |
| Microsoft.KeyVault.SecretNewVersionCreated | 创建的机密新版本 | 创建新机密或新机密版本时触发。 |
| Microsoft.KeyVault.SecretNearExpiry | 机密即将过期 | 当前版本的机密即将过期时触发。 （此事件在机密到期日期前 30 天触发。） |
| Microsoft.KeyVault.SecretExpired | 机密已过期 | 当前版本的机密过期时触发。 |
| Microsoft.KeyVault.VaultAccessPolicyChanged | 保管库访问策略已更改 | 当 Key Vault 上的访问策略发生更改时触发。 它包括将 Key Vault 权限模型更改为 Azure 基于角色的访问控制或从 Azure 基于角色的访问控制更改为 Key Vault 权限模型时的方案。   |

## <a name="event-examples"></a>事件示例

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **Microsoft.KeyVault.SecretNewVersionCreated** 的架构：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName ":"newsecret",
         "Version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **Microsoft.KeyVault.SecretNewVersionCreated** 的架构：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName ":"newsecret",
         "Version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | 对象 | 应用配置事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |


# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `type` | string | 此事件源的一个注册事件类型。 |
| `time` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | 对象 | 应用配置事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---
 

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| ---------- | ----------- |---|
| `id` | string | 触发了此事件的对象的 ID |
| `VaultName` | string | 触发了此事件的对象的密钥保管库名称 |
| `ObjectType` | string | 触发了此事件的对象的类型 |
| `ObjectName` | string | 触发了此事件的对象的名称 |
| `Version` | string | 触发了此事件的对象的版本 |
| `NBF` | number | 触发了此事件的对象的 not-before 日期（自 1970-01-01T00:00:00Z 以来的秒数） |
| `EXP` | number | 触发了此事件的对象的到期日期（自 1970-01-01T00:00:00Z 以来的秒数） |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题  |说明  |
|---------|---------|
| [通过 Azure 事件网格监视 Key Vault 事件](../key-vault/general/event-grid-overview.md) | 概述 Key Vault 与事件网格的集成。 |
| [教程：使用事件网格创建和监视密钥保管库事件](../key-vault/general/event-grid-logicapps.md) | 了解如何为 Key Vault 设置事件网格通知。 |


## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)。
* 有关如何创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关 Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../key-vault/general/overview.md)

