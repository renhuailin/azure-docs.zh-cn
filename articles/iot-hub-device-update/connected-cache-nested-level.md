---
title: 具有未经过身份验证的出站代理的 Microsoft 联网缓存两级嵌套的 Azure IoT Edge 网关 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 具有未经过身份验证的出站代理的 Microsoft 联网缓存两级嵌套的 Azure IoT Edge 网关教程
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811879"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft 联网缓存预览部署方案示例：具有未经过身份验证的出站代理的两级嵌套的 Azure IoT Edge 网关

下图介绍了这样一种方案：其中一个 Azure IoT Edge 网关具有对 CDN 资源的直接访问权限，并充当另一个 Azure IoT Edge 网关的父级。 子 IoT Edge 网关充当 Azure IoT 叶设备（如 Raspberry Pi）的父级。 Azure IoT Edge 子网关和 Azure IoT 设备都与 Internet 隔离。 下面的示例演示了两级 Azure IoT Edge 网关的配置，但对于 Microsoft 联网缓存将支持的上游主机深度没有限制。 与先前示例中的 Microsoft 联网缓存容器创建选项没有区别。

有关配置 Azure IoT Edge 网关的分层部署的更多详细信息，请参阅[连接下游 IoT Edge 设备 - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11)文档。 另外请注意，在部署 Azure IoT Edge、Microsoft 联网缓存和自定义模块时，所有模块都必须位于同一个容器注册表中。

>[!Note]
>在部署 Azure IoT Edge、Microsoft 联网缓存和自定义模块时，所有模块都必须位于同一个容器注册表中。

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft 联网缓存嵌套" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>父级网关配置
1. 在 Azure IoT 中心将 Microsoft 联网缓存模块添加到 Azure IoT Edge 网关设备部署中（有关如何获取模块的详细信息，请参阅[对离线设备的支持](connected-cache-disconnected-device-update.md)）。
2. 添加用于部署的环境变量。 下面是环境变量的示例。

    **环境变量**

    | 名称                          | “值”                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | 请参阅[环境变量](connected-cache-configure.md)说明 |
    | CUSTOMER_ID                   | 请参阅[环境变量](connected-cache-configure.md)说明 |
    | CUSTOMER_KEY                  | 请参阅[环境变量](connected-cache-configure.md)说明 |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. 添加用于部署的容器创建选项。 与上一个示例中的 MCC 容器创建选项没有区别。 下面是容器创建选项的示例。

### <a name="container-create-options"></a>容器创建选项

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>子级网关配置

>[!Note]
>如果已在自己的专用注册表中的配置中使用了复制的容器，则需要在模块部署中修改 config.toml 设置和运行时设置。 有关详细信息，请参阅[连接下游 IoT Edge 设备 - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices)。


1. 修改 Edge 代理的映像路径，如以下示例中所示：

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. 在 Azure IoT Edge 部署中修改 Edge 中心和 Edge 代理运行时设置，如以下示例中所示：
    
    * 在“Edge 中心”下的映像字段中输入 ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * 在“Edge 代理”下的映像字段中输入 ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. 在 Azure IoT 中心将 Microsoft 联网缓存模块添加到 Azure IoT Edge 网关设备部署中。

   * 为模块选择一个名称：```ConnectedCache```
   * 修改映像 URI：```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. 添加在父级部署中使用的一组相同环境变量和容器创建选项。
>[!Note]
>CACHE_NODE_ID 应该是唯一的。  CUSTOMER_ID 和 CUSTOMER_KEY 值将与父级相同。 （请参阅[配置 Microsoft 联网缓存](connected-cache-configure.md)

若要验证 Microsoft 联网缓存是否正常工作，请在托管模块的 IoT Edge 设备或网络上的任何设备的终端中执行以下命令。 将 \<Azure IoT Edge Gateway IP\> 替换为 IoT Edge 网关的 IP 地址或主机名。 （请参阅环境变量详细信息，了解此报表的可见性）。

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```