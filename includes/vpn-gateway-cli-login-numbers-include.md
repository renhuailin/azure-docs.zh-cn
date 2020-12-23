---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755444"
---
1. 使用 [az login](/cli/azure/) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 有关登录的详细信息，请参阅 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。

   ```azurecli
   az login
   ```
2. 如果有多个 Azure 订阅，请列出该帐户的订阅。

   ```azurecli
   az account list --all
   ```
3. 指定要使用的订阅。

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
