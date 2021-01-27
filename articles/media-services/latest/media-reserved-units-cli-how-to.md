---
title: 如何使用 CLI 缩放媒体保留单位 (Mru) -Azure
description: 本主题演示如何使用 CLI 通过 Azure 媒体服务来缩放媒体处理能力。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 41026a64d6f7549c5a268493cfb5975838002df2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895299"
---
# <a name="how-to-scale-media-reserved-units"></a>如何缩放媒体保留单位

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何缩放媒体保留单位 (MRS) 以加快编码速度。

## <a name="prerequisites"></a>必备条件

[创建媒体服务帐户](./create-account-howto.md)。

了解 [媒体保留单位](concept-media-reserved-units.md)。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 缩放媒体预留单位

运行 `mru` 命令。

以下 [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) 命令使用 **count** 和 **type** 参数在“amsaccount”帐户上设置媒体预留单位。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>计费

根据在帐户中预配的媒体预留单位的分钟数计费。 这与帐户中是否有作业运行无关。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="next-step"></a>后续步骤

[分析视频](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>另请参阅

* [配额和限制](limits-quotas-constraints.md)
