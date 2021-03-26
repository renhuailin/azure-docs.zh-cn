---
title: 使用 Azure CLI 列出、更新和删除映像资源
description: 使用 Azure CLI 列出、更新和删除共享映像库中的映像资源。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3554eccf28c5abbe9741af5b9718fb83710dab38
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553623"
---
# <a name="list-update-and-delete-image-resources"></a>列出、更新和删除映像资源 

可以使用 Azure CLI 管理共享映像库资源。

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>后续步骤

[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./linux/image-builder-gallery-update-image-version.md)。