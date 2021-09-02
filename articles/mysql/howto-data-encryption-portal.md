---
title: 数据加密 - Azure 门户 - Azure Database for MySQL
description: 了解如何使用 Azure 门户为 Azure Database for MySQL 设置和管理数据加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5522f07ce9543af330b3526c4021a5916a3c80df
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674445"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>使用 Azure 门户为 Azure Database for MySQL 进行数据加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何使用 Azure 门户为 Azure Database for MySQL 设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure Key Vault 中，创建密钥保管库和用于客户托管密钥的密钥。
* 该密钥保管库必须具有以下属性才能用作客户管理的密钥：
  * [软删除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 保留天数设置为 90 天
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 该密钥必须具有以下属性才能用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 执行 **get**、**wrap**、**unwrap** 操作
  * recoverylevel 属性设置为 **Recoverable**（这要求启用软删除，并将保留期设置为 90 天）
  * 已启用清除保护

可以使用以下命令来验证密钥的上述属性：

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```
* “Azure Database for MySQL - 单一服务器”应位于常规用途或内存优化定价层和常规用途存储 v2 上。 在继续操作之前，请参阅[使用客户管理的密钥进行数据加密](concepts-data-encryption-mysql.md#limitations)的限制。
## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Key Vault 中，选择“访问策略” > “添加访问策略”。 

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="密钥保管库的屏幕快照，突出显示了“访问策略”和“添加访问策略”":::

2. 选择“密钥权限”，然后选择“Get”、“Wrap”、“Unwrap”和“Principal”（MySQL 服务器的名称）。     如果在现有主体列表中找不到你的服务器主体，则需要注册服务器主体。 如果你首次尝试设置数据加密并且失败，系统将提示你注册服务器主体。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="访问策略概述":::

3. 选择“保存”。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置数据加密

1. 在 Azure Database for MySQL 中，选择“数据加密”以设置客户管理的密钥。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了“数据加密”":::

2. 可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了数据加密选项":::

3. 选择“保存”。

4. 为确保所有文件（包括临时文件）完全加密，请重新启动服务器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原或副本服务器使用数据加密

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可通过本地或异地还原操作，或通过副本（本地/跨区域）操作来创建这个新副本。 因此，对于加密的 MySQL 服务器，可以使用以下步骤来创建加密的还原服务器。

1. 在服务器上，选择“概述” > “还原”。 

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了概述和还原":::

   或对于启用了复制的服务器，在“设置”标题下，选择“复制”。 

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了“复制”":::

2. 还原操作完成后，创建的新服务器将用主服务器的密钥进行加密。 但服务器上的功能和选项已禁用，并且服务器不可访问。 这会阻止任何数据操作，因为尚未向新服务器的标识授予访问密钥保管库的权限。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了“不可访问状态”":::

3. 要使服务器可访问，请重新验证已还原服务器上的密钥。 选择“数据加密” > “重新验证密钥”。 

   > [!NOTE]
   > 第一次尝试重新验证时将失败，因为需要为新服务器的服务主体授予密钥保管库的访问权限。 要生成服务主体，请选择“重新验证密钥”，虽然将显示错误，但会生成服务主体。 之后，请参阅本文前面的[这些步骤](#set-the-right-permissions-for-key-operations)。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Azure Database for MySQL 的屏幕截图，其中突出显示了重新验证步骤":::

   你必须授予密钥保管库对新服务器的访问权限。

4. 注册服务主体后再次重新验证密钥，服务器将恢复其正常功能。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Azure Database for MySQL 屏幕截图，显示功能已经恢复":::

## <a name="next-steps"></a>后续步骤

* [验证 Azure Database for MySQL 的数据加密](howto-data-encryption-validation.md)
* [对 Azure Database for MySQL 中的数据加密进行故障排除](howto-data-encryption-troubleshoot.md)
* [使用客户管理的密钥进行加密的概念](concepts-data-encryption-mysql.md)。
