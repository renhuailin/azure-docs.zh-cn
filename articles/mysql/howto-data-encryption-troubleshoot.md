---
title: 排查数据加密问题 - Azure Database for MySQL
description: 了解如何排查 Azure Database for MySQL 中的数据加密问题
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 95b5a7650e0990f13149daeed87da8e261ec37e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241117"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>排查 Azure Database for MySQL 中的数据加密问题

本文介绍当配置为使用客户管理的密钥进行数据加密配置时，如何识别和解决 Azure Database for MySQL 中可能发生的常见问题。

## <a name="introduction"></a>简介

在 Azure Key Vault 中将数据加密配置为使用客户管理的密钥时，服务器必须持续访问该密钥。 如果服务器在 Azure Key Vault 中失去对客户管理的密钥的访问权限，它就会拒绝所有连接，返回相应的错误消息，并在 Azure 门户中将其状态更改为“无法访问”。

如果不再需要无法访问的 Azure Database for MySQL 服务器，可以将其删除以停止产生成本。 在恢复对密钥保管库的访问权限且服务器可用之前，不允许对服务器执行其他任何操作。 在使用客户管理的密钥进行加密时，也不可能在无法访问的服务器上将数据加密选项从 `Yes`（客户管理）更改为 `No`（服务托管）。 必须手动重新验证密钥，然后才能再次访问服务器。 此操作是必要的，可以在对客户管理的密钥的权限遭撤销期间保护数据免遭未经授权的访问。

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>导致服务器无法访问的常见错误

以下错误配置会导致使用 Azure Key Vault 密钥的数据加密发生大多数的问题：

- 密钥保管库不可用或不存在：
  - 密钥保管库遭意外删除。
  - 间歇性网络错误导致密钥保管库不可用。

- 你无权访问密钥保管库或密钥不存在：
  - 密钥已过期、遭意外删除或已禁用。
  - 意外删除了 Azure Database for MySQL 实例的托管标识。
  - Azure Database for MySQL 实例的托管标识没有足够的密钥权限。 例如，权限不包括“获取”、“包装”和“解包”。
  - 对 Azure Database for MySQL 实例的托管标识权限已被撤销或删除。

## <a name="identify-and-resolve-common-errors"></a>发现和解决常见错误

### <a name="errors-on-the-key-vault"></a>密钥保管库的错误

#### <a name="disabled-key-vault"></a>已禁用密钥保管库

- `AzureKeyVaultKeyDisabledMessage`
- 解释：无法在服务器上完成操作，因为 Azure Key Vault 密钥已禁用。

#### <a name="missing-key-vault-permissions"></a>缺少密钥保管库权限

- `AzureKeyVaultMissingPermissionsMessage`
- 解释：服务器没有对 Azure Key Vault 的“获取”、“包装”和“解包”必需权限。 向具有 ID 的服务主体授予任何缺少的权限。

### <a name="mitigation"></a>缓解措施

- 确认密钥保管库中是否有客户管理的密钥。
- 标识密钥保管库，然后在 Azure 门户中转到密钥保管库。
- 确保密钥 URI 标识存在的密钥。

## <a name="next-steps"></a>后续步骤

[通过 Azure 门户对 Azure Database for MySQL 设置使用客户管理的密钥的数据加密](howto-data-encryption-portal.md)
