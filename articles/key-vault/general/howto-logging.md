---
title: 启用 Azure Key Vault 日志记录
description: 如何启用 Azure Key Vault 日志记录，以便在提供的 Azure 存储帐户中保存信息。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 75e5100c12940083626d94367bb70b280a49711b
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110665152"
---
# <a name="enable-key-vault-logging"></a>启用 Key Vault 日志记录

在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 有关该功能的完整详细信息，请参阅 [Key Vault 日志记录](logging.md)。

记录的内容：

* 所有已经过身份验证的 REST API 请求，包括由于访问权限、系统错误或错误请求而发生的失败请求。
* 对 Key Vault 本身执行的操作，包括创建、删除、设置 Key Vault 访问策略，以及更新 Key Vault 属性（例如标记）。
* 对 Key Vault 中的密钥和机密执行的操作，包括：
  * 创建、修改或删除这些密钥或机密。
  * 签名、验证、加密、解密、包装和解包密钥、获取机密、列出密钥和机密（及其版本）。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。  
* 以下情况的 Azure 事件网格通知事件：过期、即将过期和已更改的保管库访问策略（但新版本事件不会记录）。 即使密钥保管库上创建了事件订阅，也会记录事件。 有关详细信息，请参阅 [Azure Key Vault 作为事件网格源](../../event-grid/event-schema-key-vault.md)。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须满足下列要求：

* 正在使用的现有密钥保管库。  
* [Azure Cloud Shell](https://shell.azure.com) - Bash 环境。
* 足够的 Azure 存储用于保存密钥保管库日志。

在本文中，命令针对 [Cloud Shell](https://shell.azure.com) 设置了格式，并将 Bash 用作环境。

## <a name="connect-to-your-key-vault-subscription"></a>连接到 Key Vault 订阅

设置密钥日志记录的第一步是连接到包含密钥保管库的订阅。 这在有多个订阅与帐户相关联的情况下特别重要。

在 Azure CLI 中，可以使用 [az account list](/cli/azure/account#az_account_list) 命令查看所有订阅。 然后，使用 [az account set](/cli/azure/account#az_account_set) 命令连接到其中一个：

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

在 Azure PowerShell 中，可以先使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 列出订阅。 然后，使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 连接到其中一个： 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>为日志创建存储帐户

尽管可以使用现有的存储帐户来保存日志，但在此需新建一个专用于 Key Vault 日志的存储帐户。 

为了进一步简化管理，你还需使用包含该 Key Vault 的同一个资源组。 在 [Azure CLI 快速入门](quick-create-cli.md)和 [Azure PowerShell 快速入门](quick-create-powershell.md)中，此资源组名为 myResourceGroup，位置为 eastus。 在适当的情况下，请将这些值替换为自己的值。 

你还需要提供存储帐户名称。 存储帐户名称必须唯一，长度介于 3 到 24 个字符，只能使用数字和小写字母。 最后，创建 `Standard_LRS` SKU 的存储帐户。

在 Azure CLI 中，使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令。 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

在 Azure PowerShell 中使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet。 你将需要提供与资源组相对应的位置。

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

无论哪种情况，请注意存储帐户的 ID。 Azure CLI 操作在输出中返回 ID。 若要使用 Azure PowerShell 获取 ID，请使用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)，然后将输出分配给变量 `$sa`。 然后，你可以看到具有 `$sa.id` 的存储帐户。 （本文后面也使用了 `$sa.Context` 属性。）

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

存储帐户的 ID 将采用如下格式："/subscriptions/your-subscription-ID/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/your-unique-storage-account-name"。

> [!NOTE]
> 如果你决定使用现有存储帐户，该帐户必须使用与 Key Vault 相同的订阅。 该帐户必须使用 Azure 资源管理器部署模型，而不是经典部署模型。

## <a name="obtain-your-key-vault-resource-id"></a>获取密钥保管库资源 ID

在 [CLI 快速入门](quick-create-cli.md)和 [PowerShell 快速入门](quick-create-powershell.md)中，你创建了具有唯一名称的密钥。 在以下步骤中再次使用该名称。 如果忘记密钥保管库的名称，可以使用 Azure CLI [az keyvault list](/cli/azure/keyvault#az_keyvault_list) 命令或 Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) cmdlet 将其列出。

使用密钥保管库的名称查找其资源 ID。 在 Azure CLI 中，使用 [az keyvault show](/cli/azure/keyvault#az_keyvault_show) 命令。

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

在 Azure PowerShell 中，使用 [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) cmdlet。

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

密钥保管库的资源 ID 将采用如下格式："/subscriptions/your-subscription-ID/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/your-unique-keyvault-name。 在下一步骤中请注意这一点。

## <a name="enable-logging"></a>启用日志记录

可以使用 Azure CLI、Azure PowerShell 或 Azure 门户为 Key Vault 启用日志记录。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings) 命令、存储帐户 ID 和密钥保管库资源 ID，如下所示：

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

可以根据需要为日志设置保留策略，确保在指定时间后自动删除较旧的日志。 例如，可以将保留策略设置为自动删除超过 90 天的日志。

在 Azure CLI 中使用 [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) 命令。 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet，并将 `-Enabled` 标志设为 `$true`，`category` 设为 `AuditEvent`（Key Vault 日志记录的唯一类别）：

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

可以根据需要为日志设置保留策略，确保在指定时间后自动删除较旧的日志。 例如，可以将保留策略设置为自动删除超过 90 天的日志。

在 Azure PowerShell 中，使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet。

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

若要在 Azure 门户中配置诊断设置，请执行以下步骤：

1. 从“资源”窗格菜单中选择“诊断设置” 。

   :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="显示如何选择诊断设置的屏幕截图。":::

1. 选择“+ 添加诊断设置”。 

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="显示添加诊断设置的屏幕截图。":::
 
1. 为你的诊断设置选择一个名称。 若要为 Key Vault 配置 Azure Monitor 日志记录，请选择“AuditEvent”和“发送到 Log Analytics 工作区” 。 然后选择要将日志发送到的订阅和 Log Analytics 工作区。

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="“诊断设置”选项的屏幕截图。":::

    否则，请选择与你要选择的日志相关的选项。

1. 选择所需选项后，选择“保存”。

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="显示如何保存所选选项的屏幕截图。":::

---

## <a name="access-your-logs"></a>访问日志

Key Vault 日志存储在你所提供的存储帐户的“insights-logs-auditevent”容器中。 若要查看这些日志，必须下载 Blob。

首先，列出容器中的所有 Blob。  在 Azure CLI 中，使用 [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) 命令。

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

在 Azure PowerShell 中，使用 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)。 若要列出此容器中的所有 Blob，请输入：

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

在 Azure CLI 命令或 Azure PowerShell cmdlet 的输出中，你可以看到 Blob 名称的格式为 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`。 日期和时间值使用协调世界时。

由于可以使用相同的存储帐户来收集多个资源的日志，Blob 名称中的完整资源 ID 适合用于仅访问或下载所需 Blob。

但首先，下载所有 blob。 在 Azure CLI 中，使用 [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) 命令，向其传递 blob 名称以及打算用于保存结果的文件的路径。

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

在 Azure PowerShell 中，使用 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) cmdlet 获取 blob 列表。 然后将该列表发送到 [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) cmdlet，以将日志下载到所选路径。

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

在 PowerShell 中运行第二个 cmdlet 时，blob 名称中的 `/` 分隔符会在目标文件夹下创建完整的文件夹结构。 你将使用此结构下载 Blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个密钥保管库，并只想要下载其中名为 CONTOSOKEYVAULT3 的密钥保管库的日志：

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果要下载 2019 年 1 月份的所有日志，请使用 `-Blob '*/year=2019/m=01/*'`：

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

## <a name="use-azure-monitor-logs"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看 Key Vault `AuditEvent` 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。

有关详细信息，包括如何进行设置，请参阅 [Azure Monitor 中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a>后续步骤

- 关于如何解释 Key Vault 日志等概念性信息，请参阅 [Key Vault 日志记录](logging.md)。
- 有关在 .NET Web 应用程序中使用 Azure Key Vault 的教程，请参阅[从 Web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。
- 有关编程参考，请参阅 [Azure Key Vault 开发人员指南](developers-guide.md)。
