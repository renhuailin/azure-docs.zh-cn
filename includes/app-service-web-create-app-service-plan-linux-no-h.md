---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: cc44780bd9b42e00ecfb3d140486fec87c767a76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765714"
---
[!INCLUDE [resource group intro text](resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group#az_group_create) 命令创建资源组。 以下示例在“西欧”位置创建名为“myResourceGroup”的资源组。 若要查看 **基本** 层中 Linux 上的应用服务支持的所有位置，请运行 [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常在附近的区域中创建资源组和资源。 

此命令完成后，JSON 输出会显示资源组属性。