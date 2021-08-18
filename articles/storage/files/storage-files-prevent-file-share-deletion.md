---
title: 防止意外删除 - Azure 文件共享
description: 了解 Azure 文件共享的软删除，以及如何将其用于数据恢复和防止意外删除。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 023320d29eac767e62e07c58de4f8fa6ac61b61f
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117841"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>防止意外删除 Azure 文件共享
Azure 文件存储为文件共享提供软删除。 使用软删除，可在应用程序或其他存储帐户用户错误地删除了文件共享后恢复文件共享。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="how-soft-delete-works"></a>软删除的工作方式
启用 Azure 文件共享的软删除后，如果删除文件共享，它将转换为软删除状态，而不是被永久删除。 可配置软删除数据被永久删除前的可恢复时间，并在此保留期内随时取消删除共享。 取消删除之后，共享和所有内容（包括快照）将还原到删除之前的状态。 软删除仅适用于文件共享级别 - 删除的单个文件仍会被永久删除。

可以在新的或现有的文件共享上启用软删除。 此外，软删除还向后兼容，因此无需对应用程序进行任何更改即可利用软删除提供的保护。 

若要在到期时间之前永久删除处于软删除状态的文件共享，必须取消删除共享、禁用软删除，然后再次删除该共享。 然后，应重新启用软删除，因为当软删除处于禁用状态时，该存储帐户中的任何其他文件共享可能会被意外删除。

对于软删除的高级文件共享，将在存储帐户总配额计算中纳入文件共享配额（预配的文件共享大小），直至达到软删除共享的到期日期，共享被完全删除为止。

## <a name="configuration-settings"></a>配置设置

### <a name="enabling-or-disabling-soft-delete"></a>启用或禁用软删除

文件共享的软删除在存储帐户级别启用，因此软删除设置适用于存储帐户中的所有文件共享。 默认为新存储帐户启用软删除，并且可随时禁用或启用软删除。 不为现有存储帐户自动启用软删除，除非已为该存储帐户中的 Azure 文件共享配置 [Azure 文件共享备份](../../backup/azure-file-share-backup-overview.md)。 如果配置了 Azure 文件共享备份，则会在该共享的存储帐户上自动启用对 Azure 文件共享的软删除。

如果为文件共享启用软删除、删除一些文件共享，然后又禁用软删除，则在该时间段内保存的文件共享仍可供访问和恢复。 启用软删除时，还需配置保持期。

### <a name="retention-period"></a>保留期

保留期是已软删除的文件共享能够进行存储和恢复的时间期限。 对于显式删除的文件共享，保持期从删除数据时开始计时。 目前，可以指定 1 到 365 天的保留期。 可随时更改软删除保持期。 更新后的保持期仅适用于在更新保持期后删除的共享。 在更新保持期之前删除的共享的过期时间取决于删除该数据时配置的保持期。

## <a name="pricing-and-billing"></a>定价和计费

软删除时，标准文件共享和高级文件共享均按使用的容量计费，而不是按预配容量计费。 此外，处于软删除状态的高级文件共享还按快照费率计费。 而处于软删除状态的标准文件共享按常规费率计费。 不会对在配置的保持期后永久删除的数据计费。

有关 Azure 文件存储常规价格的详细信息，请参阅 [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

初次启用软删除时，建议使用较短的保持期，以便更好地了解因该功能而产生的费用变动。

## <a name="next-steps"></a>后续步骤

若要了解如何启用和使用软删除，请继续阅读[启用软删除](storage-files-enable-soft-delete.md)。
