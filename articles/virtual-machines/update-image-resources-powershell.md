---
title: 使用 PowerShell 列出、更新和删除映像资源
description: 使用 Azure PowerShell 列出、更新和删除共享映像库中的映像资源。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 91664719f8af1cd44336a7701cd561eeea6d8279
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698412"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>使用 PowerShell 列出、更新和删除映像资源 

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

可以使用 Azure PowerShell 管理共享映像库资源。

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>后续步骤

[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./linux/image-builder-gallery-update-image-version.md)。