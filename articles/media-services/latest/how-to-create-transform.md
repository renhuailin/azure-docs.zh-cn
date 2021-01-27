---
title: Azure CLI 脚本示例-创建转换
description: 转换描述了处理视频或音频文件的任务的简单工作流（通常称为“工作程序”）。 本文中的 Azure CLI 脚本演示如何创建转换。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: adbd7deccf32312f67cff7b92ff7813036e9b1b3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898522"
---
# <a name="create-a-transform"></a>创建转换

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 脚本演示如何创建转换。 转换描述了处理视频或音频文件的任务的简单工作流（通常称为“工作程序”）。 应始终检查具有所需名称和“工作程序”的转换是否已存在。 如果已存在，应再次使用该转换。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./create-account-howto.md)。

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> 只能为 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 指定自定义标准编码器预设 JSON 文件的路径，请参阅[使用自定义转换进行编码](custom-preset-cli-howto.md)示例。
>
> 使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 时，不能传递文件名。

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>后续步骤

[详细了解转换和作业](transforms-jobs-concept.md)
