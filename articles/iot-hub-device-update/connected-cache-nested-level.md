---
title: 具有未经过身份验证的出站代理的 Microsoft 联网缓存两级嵌套的 Azure IoT Edge 网关 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 具有未经过身份验证的出站代理的 Microsoft 联网缓存两级嵌套的 Azure IoT Edge 网关教程
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615374"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft 联网缓存预览部署方案示例：具有未经过身份验证的出站代理的两级嵌套的 Azure IoT Edge 网关

根据下面的关系图，在此方案中，有一个 Azure IoT Edge 网关和一个下游 Azure IoT Edge 设备、一个作为另一个 Azure IoT Edge 网关父级的 Azure IoT Edge 网关以及一个位于 IT DMZ 的代理服务器。 下面是 Microsoft 联网缓存环境变量的示例，这些变量将在 Azure 门户 UX 中针对部署到 Azure IoT Edge 网关的两个 MCC 模块进行设置。 显示的示例演示了两级 Azure IoT Edge 网关的配置，但对于 Microsoft 联网缓存将支持的上游主机深度没有限制。 与先前示例中的 MCC 容器创建选项没有区别。

有关配置 Azure IoT Edge 网关的分层部署的更多详细信息，请参阅[连接下游 IoT Edge 设备 - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true)文档。 另外请注意，在部署 Azure IoT Edge、Microsoft 联网缓存和自定义模块时，所有模块都必须位于同一个容器注册表中。

下图介绍了这样一种方案：其中一个 Azure IoT Edge 网关作为对 CDN 资源的直接访问权限充当作为 Azure IoT 叶设备（如 Raspberry Pi）的父级的另一个 Azure IoT Edge 网关的父级。 只有 Azure IoT Edge 网关父级与 CDN 资源具有 Internet 连接，并且 Azure IoT Edge 子级和 Azure IoT 设备都是与 Internet 隔离的。 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft 联网缓存嵌套" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>父级网关配置

1. 在 Azure IoT 中心将 Microsoft 联网缓存模块添加到 Azure IoT Edge 网关设备部署中。
2. 添加用于部署的环境变量。 下面是环境变量的示例。

    **环境变量**

    | 名称                 | “值”                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | 请参阅上面的环境变量说明。 |
    | CUSTOMER_ID                   | 请参阅上面的环境变量说明。 |
    | CUSTOMER_KEY                  | 请参阅上面的环境变量说明。 |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | 代理服务器 IP 或 FQDN                     |

3. 添加用于部署的容器创建选项。 与上一个示例中的 MCC 容器创建选项没有区别。 下面是容器创建选项的示例。

### <a name="container-create-options"></a>容器创建选项

```markdown
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
```

## <a name="child-gateway-configuration"></a>子级网关配置

>[!Note]
>如果已在自己的专用注册表中的配置中使用了复制的容器，则需要在模块部署中修改 config.toml 设置和运行时设置。 有关详细信息，请参阅[教程 - 创建 IoT Edge 设备的层次结构 - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device)。

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

4. 添加在父级部署中使用的相同环境变量和容器创建选项。

若要验证 Microsoft 联网缓存是否正常工作，请在托管模块的 IoT Edge 设备或网络上的任何设备的终端中执行以下命令。

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```