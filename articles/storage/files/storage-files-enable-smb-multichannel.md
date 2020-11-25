---
title: 启用 SMB 多通道
description: 了解如何在 Azure 高级文件共享上启用 SMB 多通道。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2444ec28a2618b638f78926e214de468f56c5e52
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "95995451"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>在 FileStorage 帐户上启用 SMB 多通道 (预览)  

Azure FileStorage 帐户支持 SMB 多通道 (预览) ，这可通过建立与高级文件共享的多个网络连接来提高 SMB 1.x 客户端的性能。 本文提供了在现有存储帐户上启用 SMB 多通道的分步指导。 有关 Azure 文件 SMB 多通道的详细信息，请参阅 SMB 多通道性能。

## <a name="limitations"></a>限制

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>先决条件

- [创建 FileStorage 帐户](storage-how-to-create-premium-fileshare.md)。
- 如果要使用 Azure PowerShell 模块，请 [安装该模块的3.0.1 版预览版本](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)。

## <a name="getting-started"></a>入门

打开 PowerShell 窗口并登录到你的 Azure 订阅。 在此处，可以通过以下命令注册 SMB 多通道预览版。

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> 注册可能需要长达一小时的时间。

### <a name="verify-that-feature-registration-is-complete"></a>验证功能注册已完成

由于在存储帐户上启用该功能可能需要长达一小时，因此你可能需要使用以下命令来验证是否已为你的订阅注册了此功能：

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>启用 SMB 多通道 
创建 FileStorage 帐户后，可以按照说明更新存储帐户的 SMB 多通道设置。

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. 登录到 Azure 门户，导航到要在其上配置 SMB 多通道的 FileStorage 存储帐户。
1. 选择 "**文件服务**" 下的 "**文件共享**"，然后选择 "**文件共享设置**"。
1. 将 **SMB 多通道** 切换到 (或 **关闭** 以禁用) 并选择 "**保存** **"** 。

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="存储帐户的屏幕截图，smb 多通道已开启。":::

如果在 " **文件共享设置** " 下未显示 "SMB 多通道" 选项，或者在更新配置时无法更新设置错误，请确保你的订阅已注册，并且你的帐户位于受支持的帐户类型和复制的某个 [受支持区域](#regional-availability) 中。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 模块启用 SMB 多通道，必须 [安装该模块的3.0.1 预览版本](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) 。

`$resourceGroupName`运行以下 PowerShell 命令之前，请将变量和设置 `$storageAccountName` 为资源组和存储帐户。

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI 尚不支持配置 SMB 多通道。 请参阅门户说明，在存储帐户上配置 SMB 多通道。

---

> [!NOTE]
> 对 SMB 多通道配置设置的任何更改都将应用于存储帐户下的所有文件共享。 不过，需要在客户端上重新装载共享以使更改生效。


## <a name="next-steps"></a>后续步骤 

- 重新[装载文件共享](storage-how-to-use-files-windows.md)以利用 SMB 多通道。
- [解决与 SMB 多通道相关的任何问题](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)。
- 若要了解有关改进的详细信息，请参阅 [SMB 多通道性能](storage-files-smb-multichannel-performance.md)
 - 若要了解有关 Windows SMB 多通道功能的详细信息，请参阅 [管理 SMB 具有](/azure-stack/hci/manage/manage-smb-multichannel)。
