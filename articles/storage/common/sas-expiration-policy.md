---
title: 为共享访问签名创建过期策略
titleSuffix: Azure Storage
description: 在存储帐户上创建一个策略，用于定义共享访问签名 (SAS) 的有效期。 了解如何监视策略违规以修正安全风险。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709057"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>为共享访问签名创建过期策略

可以使用共享访问签名 (SAS) 来委托对 Azure 存储帐户中资源的访问权限。 SAS 令牌包括目标资源、授予的权限以及允许的访问间隔。 最佳做法建议你限制 SAS 的间隔，以防 SAS 泄露。 通过为存储帐户设置 SAS 过期策略，可以在用户创建 SAS 时提供建议的过期时间上限。

SAS 过期策略不会阻止用户创建过期时间超过策略所建议的限制的 SAS。 当用户创建违反策略的 SAS 时，他们会看到警告以及建议的最大间隔。 如果为 Azure Monitor 的日志记录配置了诊断设置，则每当用户创建在建议的间隔后过期的 SAS 时，Azure 存储就会写入到日志中的属性。

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)。

## <a name="create-a-sas-expiration-policy"></a>创建 SAS 到期策略

在存储帐户上创建 SAS 过期策略时，该策略将应用于可在该存储帐户上创建的每种 SAS，包括服务 SAS、用户委托 SAS 或帐户 SAS。

若要为存储帐户配置 SAS 过期策略，请使用 Azure 门户、PowerShell 或 Azure CLI。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

若要在 Azure 门户中创建 SAS 过期策略，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下，选择“配置”。
1. 找到“允许对共享访问签名(SAS)过期间隔使用建议的上限”设置，将其设置为“已启用” 。
1. 为在此存储帐户中的资源上创建的任何新共享访问签名指定建议的间隔。

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="显示如何在 Azure 门户中配置 SAS 过期策略的屏幕截图":::

1. 选择“保存”按钮保存更改。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要创建 SAS 过期策略，请使用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 命令，然后将 `-SasExpirationPeriod` 参数设置为从签署 SAS 的时间起 SAS 令牌可处于活动状态的天数、小时数、分钟数和秒数。 为 `-SasExpirationPeriod` 参数提供的字符串使用以下格式：`<days>.<hours>:<minutes>:<seconds>`。 例如，如果你希望 SAS 在其签署后的 1 天 12 小时 5 分 6 秒过期，则可以使用字符串 `1.12:05:06`。

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> 还可以在创建存储帐户时，通过设置 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令的 `-SasExpirationPeriod` 参数来设置 SAS 过期策略。

若要验证已应用的策略，请使用上述命令中返回到 `$account` 变量的 [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) 的 `SasPolicy` 属性。 
  
```powershell
$account.SasPolicy
```

SAS 有效期将显示在控制台输出中。

> [!div class="mx-imgBorder"]
> ![SAS 有效期](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要创建 SAS 过期策略，请使用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 命令，然后将 `--key-exp-days` 参数设置为从签署 SAS 的时间起 SAS 令牌可处于活动状态的天数、小时数、分钟数和秒数。 为 `--key-exp-days` 参数提供的字符串使用以下格式：`<days>.<hours>:<minutes>:<seconds>`。 例如，如果你希望 SAS 在其签署后的 1 天 12 小时 5 分 6 秒过期，则可以使用字符串 `1.12:05:06`。

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> 还可以在创建存储帐户时，通过设置 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令的 `--key-exp-days` 参数来设置 SAS 过期策略。

若要验证已应用的策略，请调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令，并在 `-query` 参数中使用字符串 `{SasPolicy:sasPolicy}`。
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

SAS 有效期将显示在控制台输出中。

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>查询日志以获取策略违规信息

若要记录其有效期比 SAS 过期策略建议的间隔更长的 SAS 的创建操作，请先创建一个诊断设置，用于将日志发送到 Azure Log Analytics 工作区。 有关详细信息，请参阅[将日志发送到 Azure Log Analytics](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics)。

接下来，使用 Azure Monitor 日志查询来确定 SAS 是否已过期。 在 Log Analytics 工作区中创建新查询，添加以下查询文本，然后按“运行”。

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>另请参阅

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
- [创建服务 SAS](/rest/api/storageservices/create-service-sas)
- [创建用户委托 SAS](/rest/api/storageservices/create-user-delegation-sas)
- [创建帐户 SAS](/rest/api/storageservices/create-account-sas)