---
title: " (Mru) CLI 缩放媒体保留单位"
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
ms.openlocfilehash: a07c4a20b854e09daf3b320b8c99757ca99b2578
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213804"
---
# <a name="how-to-scale-media-reserved-units"></a>如何缩放媒体保留单位

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何缩放媒体保留单位 (MRS) 以加快编码速度。

## <a name="prerequisites"></a>必备条件

[创建媒体服务帐户](./create-account-howto.md)。

了解 [媒体保留单位](concept-media-reserved-units.md)。

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 缩放媒体预留单位

运行 `mru` 命令。

以下 [az ams account mru](/cli/azure/ams/account/mru) 命令使用 **count** 和 **type** 参数在“amsaccount”帐户上设置媒体预留单位。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>计费

根据在帐户中预配的媒体预留单位的分钟数计费。 这与帐户中是否有作业运行无关。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="next-step"></a>后续步骤

[分析视频](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>另请参阅

* [配额和限制](limits-quotas-constraints.md)
