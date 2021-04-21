---
title: 滚动更新存储访问密钥后更新媒体服务 v3 | Microsoft Docs
description: 本文提供有关在滚动更新存储访问密钥后如何更新媒体服务 v3 的指导。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281197"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>滚动更新存储访问密钥后更新媒体服务 v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

当你创建新的 Azure 媒体服务 (AMS) 帐户时，系统会要求你选择一个 Azure 存储帐户。  可将多个存储帐户添加到媒体服务帐户。 本文介绍如何轮换存储密钥。 此外，介绍如何将存储帐户添加到媒体帐户。

若要完成本文所述的操作，应使用 [Azure 资源管理器 API](/rest/api/media/operations/azure-media-services-rest-api-reference) 和 [PowerShell](/powershell/module/az.media)。  有关详细信息，请参阅[如何使用 PowerShell 和 Resource Manager 管理 Azure 资源](../../azure-resource-manager/management/manage-resource-groups-powershell.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>生成存储访问密钥

创建新的存储帐户后，Azure 将生成两个 512 位存储访问密钥，用于对访问你的存储帐户的操作进行身份验证。 为了保持存储连接的较高安全性，请定期重新生成并轮换存储访问密钥。 将提供两个访问密钥（主密钥和辅助密钥），以便在重新生成其中一个访问密钥时，始终能够使用另一个访问密钥连接到存储帐户。 此过程也称为“轮转访问密钥”。

媒体服务依赖于为它提供的存储密钥。 具体而言，用于流式传输或下载资产的定位符依赖于指定的存储访问密钥。 创建 AMS 帐户后，该帐户默认依赖于主存储访问密钥。 但是，用户可以更新 AMS 的存储密钥。 必须执行以下步骤来让媒体服务知道要使用哪一个密钥：

>[!NOTE]
> 如果有多个存储帐户，请对每个存储帐户执行此过程。 存储密钥的轮换顺序不是固定的。 可以先轮换辅助密钥，再轮换主密钥，反之亦然。
>
> 在生产帐户中执行这些步骤之前，请确保在生产前帐户中测试这些步骤。
>

## <a name="steps-to-rotate-storage-keys"></a>轮换存储密钥的步骤
 
 1. 通过 PowerShell cmdlet 或 [Azure](https://portal.azure.com/) 门户更改存储帐户主密钥。
 2. 结合相应的参数调用 `Sync-AzMediaServiceStorageKeys` cmdlet 以强制媒体帐户选择存储帐户密钥
 
    以下示例演示如何将密钥同步到存储帐户。
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. 等待大约一小时。 验证流式处理方案是否正常工作。
 4. 通过 PowerShell cmdlet 或 Azure 门户更改存储帐户辅助密钥。
 5. 结合相应的参数调用 `Sync-AzMediaServiceStorageKeys` PowerShell 以强制媒体帐户选择新的存储帐户密钥。
 6. 等待大约一小时。 验证流式处理方案是否正常工作。
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell cmdlet 示例

以下示例演示如何获取存储帐户并将它与 AMS 帐户同步。

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>将存储帐户添加到 AMS 帐户的步骤

下文介绍了如何将存储帐户添加到 AMS 帐户：[将多个存储帐户附加到媒体服务帐户](storage-managing-multiple-storage-accounts-how-to.md)。
