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
ms.openlocfilehash: 923620c1c9719d857cc848507a4c5507f528d34e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559345"
---
### <a name="to-view-local-network-gateways"></a>查看本地网关

若要查看本地网关的列表，请使用 [az network local-gateway list](/cli/azure/network/local-gateway) 命令。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>验证共享密钥值

验证共享密钥值与用于 VPN 设备配置的值是否相同。 如果不同，请使用设备提供的值再次运行链接，或者使用返回的值更新设备。 值必须匹配。 若要查看共享的密钥，请使用 [az network vpn-connection-list](/cli/azure/network/vpn-connection)。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>查看 VPN 网关的公共 IP 地址

若要查找虚拟网关的公共 IP 地址，请使用 [az network public-ip list](/cli/azure/network/public-ip) 命令。 为了方便阅读，对本示例的输出进行了格式化，以表格式显示一系列公共 IP。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```