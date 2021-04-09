---
title: 重置帐户凭据 - CLI
description: 使用 Azure CLI 脚本重置帐户凭据和恢复 app.config 设置。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091835"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI 示例：重置帐户凭据

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文中的 Azure CLI 脚本演示如何重置帐户凭据和恢复 app.config 设置。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./create-account-howto.md)。

## <a name="example-script"></a>示例脚本

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>后续步骤

* [az ams](/cli/azure/ams)
* [重置凭据](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
