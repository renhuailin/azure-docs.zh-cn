---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569499"
---
默认情况下，标准文件共享不得超过 5 TiB，但你可将共享上限提高到 100 TiB。 若要提高共享上限，请在存储帐户上启用“大型文件共享”。 高级存储帐户（FileStorage 存储帐户）没有“大型文件共享”功能标志，原因是所有高级文件共享都已经可以预配到全容量 100 TiB。

仅可在本地冗余或区域冗余标准存储帐户上启用大型文件共享功能。 启用大型文件共享功能标志后，无法将冗余级别更改为异地冗余或异地区域冗余存储。

若要在现有存储帐户上启用“大型文件共享”功能，请在存储帐户的目录中导航到“文件共享”。
在此边栏选项卡上，选择“共享容量”，将共享容量更改为“100 TiB”，然后选择“保存”  。

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="显示 Azure 门户中的“启用大型文件共享”选择加入设置的屏幕截图。" lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

还可通过 PowerShell cmdlet [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) 和 Azure CLI 命令 [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) 来启用 100 TiB 文件共享。 有关启用大型文件共享的详细说明，请参阅[启用和创建大型文件共享](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

要详细了解如何在新存储帐户上创建大型文件共享，请参阅[创建 Azure 文件共享](../articles/storage/files/storage-how-to-create-file-share.md)。