---
title: Microsoft 联网缓存预览部署方案示例 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 联网缓存预览部署方案示例教程
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658673"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Microsoft 联网缓存预览部署方案示例

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>单级别 Azure IoT Edge 网关无代理

下图描述了一种方案，其中 Azure IoT Edge 网关可以直接访问 CDN 资源，并且存在 Azure IoT 叶设备（例如 Raspberry PI），它是 Azure IoT Edge 网关的与 Internet 隔离的子设备。 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft 联网缓存已断开连接的设备更新" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. 在 Azure IoT 中心将 Microsoft 联网缓存模块添加到 Azure IoT Edge 网关设备部署中（有关如何获取模块的详细信息，请参阅 `MCC concepts`）。
2. 添加用于部署的环境变量。 下面是环境变量的示例。

    **环境变量**
    
    | 名称                 | “值”                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | 请参阅上面的环境变量说明。 |
    | CUSTOMER_ID                   | 请参阅上面的环境变量说明。 |
    | CUSTOMER_KEY                  | 请参阅上面的环境变量说明。 |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. 添加用于部署的容器创建选项。 下面是容器创建选项的示例。

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

若要验证 Microsoft 联网缓存是否正常工作，请在托管模块的 IoT Edge 设备或网络上的任何设备的终端中执行以下命令。

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>具有未经过身份验证的出站代理的单级别 Azure IoT Edge 网关

在此方案中，有一个 Azure IoT Edge 网关，该网关有权通过未经过身份验证的出站代理访问 CDN 资源。 正在将 Microsoft 联网缓存配置为缓存自定义存储库中的内容，并且网络上的任何人都可以看到摘要报告。 下面是要设置的 MCC 环境变量的示例。

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft 联网缓存单级别代理" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

若要验证 Microsoft 联网缓存是否正常工作，请在托管模块的 Azure IoT Edge 设备或网络上的任何设备的终端中执行以下命令。

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
