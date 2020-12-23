---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217916"
---
可使用 [az network vpn-connection show](/cli/azure/network/vpn-connection) 命令来验证连接是否成功。 在此示例中，“ --Name”是指要测试的连接的名称。 当连接处于建立过程中时，连接状态会显示“正在连接”。 建立连接后，状态更改为“已连接”。

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
