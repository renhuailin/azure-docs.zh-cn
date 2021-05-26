---
title: 故障排除 - Azure 事件网格 IoT Edge | Microsoft Docs
description: IoT Edge 上事件网格的故障排除。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 9b8e5b95b0d1853d81de5a4ec603a3a59563da9d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379793"
---
# <a name="common-issues"></a>常见问题

如果在环境中使用 IoT Edge 上的 Azure 事件网格时遇到问题，请使用本文作为指南来排除故障并解决问题。

## <a name="view-event-grid-module-logs"></a>查看事件网格模块日志

若要排除故障，可能需要访问事件网格模块日志。 请在部署该模块的 VM 上运行以下命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

在 Linux 版上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>无法发出 HTTPS 请求

* 首先确保事件网格模块的 **inbound:serverAuth:tlsPolicy** 设置为“**严格**”或“**启用**”。

* 如果是模块间的通信，请确保在端口 4438 上进行调用，并且模块的名称与部署的名称相匹配。 

  例如，如果事件网格模块用名称 eventgridmodule 部署，则 URL 应为 https://eventgridmodule:4438 。 请确保大小写和端口号正确。
    
* 如果它来自非 IoT 模块，请确保在部署期间将事件网格端口映射到主机计算机，例如，

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>无法发出 HTTPS 请求

* 首先确保事件网格模块的 **inbound:serverAuth:tlsPolicy** 设置为“**启用**”或“**禁用**”。

* 如果是模块间的通信，请确保在端口 5888 上进行调用，并且模块的名称与部署的名称相匹配。 

  例如，如果事件网格模块用名称 eventgridmodule 部署，则 URL 应为 http://eventgridmodule:5888 。 请确保大小写和端口号正确。
    
* 如果它来自非 IoT 模块，请确保在部署期间将事件网格端口映射到主机计算机，例如，

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>证书链由不受信任的颁发机构颁发

默认情况下，事件网格模块配置为使用 IoT Edge 安全性守护程序颁发的证书对客户端进行身份验证。 请确保客户端正在提供一个根为此链的证书。

[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) 中的 **IoTSecurity** 类将演示如何从 IoT Edge 安全性守护程序检索证书，并使用该证书来配置传出呼叫。

如果是非生产环境，则可以选择禁用客户端身份验证。 有关详细信息，请参阅[安全性和身份验证](security-authentication.md)。

## <a name="debug-events-not-received-by-subscriber"></a>订阅服务器未收到调试事件

这种情况的典型原因如下：

* 此事件从未成功发布。 将事件发布到事件网格模块时，客户端应收到值为 200（确定）的 HTTP StatusCode。

* 检查事件订阅以验证：
    * 终结点 URL 有效
    * 订阅中的任何筛选器都不会导致事件“删除”。

* 验证订阅服务器模块是否正在运行

* 登录到部署了事件网格模块的 VM，并查看其日志。

* 通过设置 **broker:logDeliverySuccess=true**，重新部署事件网格模块并重试请求，打开每个交付日志记录。 每次交付都打开日志记录可能会影响吞吐量和延迟，这样一旦调试完成，我们便建议将此改回 **broker:logDeliverySuccess=false**，并重新部署事件网格模块。

* 通过设置 **metrics:reportertype=console** 来打开指标，并重新部署事件网格模块。 此后的任何操作都会导致指标记录在事件网格模块的控制台上，该模块可用于进一步调试。 建议仅打开用于调试的指标，并在完成后通过设置 **metrics:reportertype=none** 并重新部署事件网格模块将其关闭。

## <a name="next-steps"></a>后续步骤

使用 IoT Edge 上的事件网格在 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) 上报告任何问题或建议。