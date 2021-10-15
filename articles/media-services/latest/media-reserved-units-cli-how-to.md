---
title: 缩放媒体保留单位 (MRU) CLI
description: 本主题演示如何使用 CLI 通过 Azure 媒体服务来缩放媒体处理能力。
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 2525417516691fa4ffb6d681fc23b394f4588649
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129364096"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>如何缩放媒体保留单位（旧版）

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何缩放媒体保留单位 (MRU) 以加快编码速度。

> [!WARNING]
> 对于使用 API 的 2020-05-01（或更高）版本或更高版本创建的媒体服务帐户，此命令将不再适用。 这些帐户将不再需要媒体保留单位，因为系统会根据负载自动纵向扩展和缩减。 如果在 Azure 门户中看不到管理 MRU 的选项，则说明正在运行使用 2020-05-01 API 或更高版本创建的帐户。
> 本文的目的是记录使用 MRU 的传统过程

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

 虽然之前对媒体保留单位收费，但自 2021 年 4 月 17 日起，对那些为媒体保留单位提供配置的帐户不再收取任何费用。

## <a name="see-also"></a>另请参阅

* [从媒体服务 v2 迁移到 v3](migrate-v-2-v-3-migration-introduction.md)
