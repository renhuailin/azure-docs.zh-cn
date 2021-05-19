---
title: 缩放媒体保留单位 (MRU) CLI
description: 本主题演示如何使用 CLI 通过 Azure 媒体服务来缩放媒体处理能力。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121670"
---
# <a name="how-to-scale-media-reserved-units"></a>如何缩放媒体保留单位

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何缩放媒体保留单位 (MRS) 以加快编码速度。

> [!WARNING]
> 对于使用 API 的 2020-05-01 版本或更高版本创建的媒体服务帐户，此命令将不再适用。 这些帐户将不再需要媒体保留单位，因为系统会根据负载自动纵向扩展和缩减。 如果在 Azure 门户中看不到管理 MRU 的选项，则说明正在运行使用 2020-05-01 API 或更高版本创建的帐户。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./account-create-how-to.md)。

了解[媒体保留单位](concept-media-reserved-units.md)。

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 缩放媒体预留单位

运行 `mru` 命令。

以下 [az ams account mru](/cli/azure/ams/account/mru) 命令使用 **count** 和 **type** 参数在“amsaccount”帐户上设置媒体预留单位。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>计费

根据在帐户中预配的媒体预留单位的分钟数计费。 这与帐户中是否有作业运行无关。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="next-step"></a>后续步骤

[分析视频](analyze-videos-tutorial.md)

## <a name="see-also"></a>另请参阅

* [配额和限制](limits-quotas-constraints-reference.md)
