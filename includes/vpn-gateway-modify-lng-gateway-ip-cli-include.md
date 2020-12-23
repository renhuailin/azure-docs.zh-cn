---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027079"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>修改本地网关的“gatewayIpAddress”

如果要连接的 VPN 设备已更改其公共 IP 地址，则需根据该更改修改本地网关。 可以更改网关 IP 地址而不删除现有的 VPN 网关连接（如果有）。 要修改网关 IP 地址，请使用 [az network local-gateway update](/cli/azure/network/local-gateway) 命令将值“Site2”和“TestRG1”替换为自己的值。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

验证输出中的 IP 地址是否正确：

```
"gatewayIpAddress": "23.99.222.170",
```