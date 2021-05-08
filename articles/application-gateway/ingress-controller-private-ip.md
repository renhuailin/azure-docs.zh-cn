---
title: 使用入口终结点的专用 IP 地址进行内部路由
description: 本文介绍如何使用专用 IP 进行内部路由，以便将群集中的入口终结点公开给 VNet 的其余部分。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397302"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>使用入口终结点的专用 IP 进行内部路由 

此功能允许使用专用 IP 在 `Virtual Network` 中公开入口终结点。

## <a name="pre-requisites"></a>先决条件  
使用[专用 IP 配置](./configure-application-gateway-with-private-frontend-ip.md)的应用程序网关

可通过两种方法配置控制器以使用入口专用 IP

## <a name="assign-to-a-particular-ingress"></a>分配到特定入口
若要通过专用 IP 公开特定入口，请在入口中使用注释 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip)。

### <a name="usage"></a>使用情况
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

对于没有专用 IP 的应用程序网关，将忽略使用 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 进行注释的入口。 这会在入口事件和 AGIC Pod 日志中指示。

* 错误，如入口事件中指示的那样

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* 错误，如 AGIC 日志中指示的那样

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>全局分配
如果要求是限制所有入口通过专用 IP 进行公开，请在 `helm` 配置中使用 `appgw.usePrivateIP: true`。

### <a name="usage"></a>使用情况
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

在应用程序网关上配置前端侦听器时，这会使得入口控制器筛选专用 IP 的 IP 地址配置。
如果 `usePrivateIP: true` 且没有分配任何专用 IP，则 AGIC 会死机并崩溃。

> [!NOTE]
> 应用程序网关 v2 SKU 要求公共 IP。 如果要求应用程序网关为专用网关，请附加 [`Network Security Group`](../virtual-network/network-security-groups-overview.md) 到应用程序网关的子网来限制流量。