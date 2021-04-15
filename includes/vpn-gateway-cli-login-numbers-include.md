---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devex-tag-azurecli
ms.openlocfilehash: 1a22c63eb01abc3775b2bf299d8464323e69c372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386712"
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
