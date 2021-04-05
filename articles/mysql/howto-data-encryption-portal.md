---
title: 数据加密 - Azure 门户 - Azure Database for MySQL
description: 了解如何通过 Azure 门户为 Azure Database for MySQL 设置和管理数据加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00670746c1686bca354adc989ddce6c9dd336491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96519053"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>通过 Azure 门户进行 Azure Database for MySQL 数据加密

了解如何通过 Azure 门户为 Azure Database for MySQL 设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 在 Azure Key Vault 中，创建要用于客户管理的密钥的密钥保管库和密钥。
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
  * 执行“获取”、“包装”、“解包”操作
  * recoverylevel 属性设置为“可恢复”（这需要启用软删除，保留期设置为 90 天）
  * 已启用清除保护

可以通过使用以下命令验证密钥的以上属性：

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 在 Key Vault 中，选择“访问策略” > “添加访问策略”。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="突出显示了“访问策略”和“添加访问策略”的 Key Vault 的屏幕截图":::

2. 选择“密钥权限”，然后选择“获取”、“换行”、“解包”和“主体”（即 MySQL 服务器的名称）。 如果在现有主体列表中找不到服务器主体，则需要注册它。 首次尝试设置数据加密时，系统会提示你注册服务器主体，但失败了。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="访问策略概述":::

3. 选择“保存”。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置数据加密

1. 在 Azure Database for MySQL 中，选择“数据加密”以设置客户管理的密钥。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="突出显示了数据加密的 Azure Database for MySQL 的屏幕截图":::

2. 可以选择密钥保管库和密钥对，也可以输入密钥标识符。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="突出显示了数据加密选项的 Azure Database for MySQL 的屏幕截图":::

3. 选择“保存”。

4. 若要确保所有文件（包括临时文件）完全加密，请重新启动服务器。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原服务器或副本服务器使用数据加密

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可通过本地或异地还原操作，或通过副本（本地/跨区域）操作创建这个新副本。 因此，对于加密的 MySQL 服务器，可以使用以下步骤创建加密的还原服务器。

1. 在服务器上，选择“概述” > “还原”。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="突出显示了概述和还原的 Azure Database for MySQL 的屏幕截图":::

   或者，对于启用了复制的服务器，在“设置”标题下选择“复制”。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="突出显示了复制的 Azure Database for MySQL 的屏幕截图":::

2. 还原操作完成后，使用主服务器的密钥加密已创建的新服务器。 但服务器上的功能和选项已禁用，并且服务器不可访问。 这会阻止任何数据操作，因为尚未向新服务器的标识授予访问密钥保管库的权限。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="突出显示了不可访问状态的 Azure Database for MySQL 的屏幕截图":::

3. 若要使服务器可访问，请重新验证已还原服务器上的密钥。 选择“数据加密” > “重新验证密钥”。

   > [!NOTE]
   > 第一次尝试重新验证将失败，因为需要向新服务器的服务主体授予对密钥保管库的访问权限。 若要生成服务主体，请选择“重新验证密钥”，此时将显示错误，但会生成服务主体。 之后，请参阅本文前面的[这些步骤](#set-the-right-permissions-for-key-operations)。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="突出显示了重新验证步骤的 Azure Database for MySQL 的屏幕截图":::

   你将需要向密钥保管库授予对新服务器的访问权限。

4. 注册服务主体后，再次重新验证密钥，服务器随后会恢复其正常功能。

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="显示已还原功能的 Azure Database for MySQL 的屏幕截图":::

## <a name="next-steps"></a>后续步骤

 若要详细了解数据加密，请参阅[使用客户管理的密钥进行 Azure Database for MySQL 数据加密](concepts-data-encryption-mysql.md)。
