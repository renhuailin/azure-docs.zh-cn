---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: e16e3dc6788fb30a87a78fd6d6077087f461cfa7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511334"
---
<!--Create a media services asset CLI-->

以下 Azure CLI 命令创建新的媒体服务资产。 将 `assetName`、`amsAccountName` 和 `resourceGroup` 值替换为当前正在使用的值。

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
