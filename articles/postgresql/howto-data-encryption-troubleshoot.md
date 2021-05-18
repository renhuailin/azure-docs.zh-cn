---
title: 数据加密故障排除 - Azure Database for PostgreSQL - 单一服务器
description: 了解如何排查 Azure Database for PostgreSQL - 单一服务器上数据加密的问题
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: c315e1df473f3d23bab7e2a78ce166f22272ee70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242239"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的数据加密故障排除

本文将帮助你确定和解决 Azure Database for PostgreSQL 的单服务器部署在使用客户管理的密钥配置了数据加密时可能出现的常见问题。

## <a name="introduction"></a>简介

在将数据加密配置为使用 Azure Key Vault 中客户管理的密钥时，服务器需要持续访问该密钥。 如果服务器失去对 Azure Key Vault 中客户管理的密钥的访问权限，就会拒绝所有连接，返回相应的错误消息，并将它在 Azure 门户中的状态更改为“无法访问”。

如果不再需要无法访问的 Azure Database for PostgreSQL 服务器，可以将其删除以停止产生费用。 在密钥保管库的访问权限得到恢复且服务器可用之前，不允许在服务器上进行其他操作。 如果使用客户管理的密钥加密数据，也无法在无法访问的服务器上将数据加密选项从 `Yes`（客户管理的）更改为 `No`（服务管理的）。 必须手动重新验证密钥，然后才能再次访问服务器。 此操作是必要操作，可以在客户管理的密钥的权限遭撤销期间保护数据免遭未经授权的访问。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>导致服务器无法访问的常见错误

使用 Azure Key Vault 密钥进行数据加密时的大多数问题都是以下错误配置导致的：

- 密钥保管库不可用或不存在：
  - 密钥保管库遭意外删除。
  - 间歇性网络错误导致密钥保管库不可用。

- 无权访问密钥保管库或密钥不存在：
  - 密钥已过期或意外删除或禁用。
- 意外删除了 Azure Database for PostgreSQL 实例的托管标识。
  - Azure Database for PostgreSQL 实例的托管标识没有足够的密钥权限。 例如，权限不包括获取、包装和解包。
  - Azure Database for PostgreSQL 实例的托管标识权限已被撤销或删除。

## <a name="identify-and-resolve-common-errors"></a>发现和解决常见错误

### <a name="errors-on-the-key-vault"></a>密钥保管库出现错误

#### <a name="disabled-key-vault"></a>密钥保管库被禁用

- `AzureKeyVaultKeyDisabledMessage`
- 说明：无法在服务器上完成操作，因为 Azure Key Vault 密钥被禁用。

#### <a name="missing-key-vault-permissions"></a>缺少密钥保管库权限

- `AzureKeyVaultMissingPermissionsMessage`
- 说明：服务器没有访问 Azure Key Vault 所必需的获取、包装和解包权限。 向有 ID 的服务主体授予任何缺少的权限。

### <a name="mitigation"></a>缓解措施

- 确认密钥保管库中存在客户管理的密钥。
- 标识密钥保管库，然后在 Azure 门户中转到密钥保管库。
- 确保密钥 URI 标识了存在的密钥。

## <a name="next-steps"></a>后续步骤

[使用 Azure 门户在 Azure Database for PostgreSQL 上用客户管理的密钥设置数据加密](howto-data-encryption-portal.md)
