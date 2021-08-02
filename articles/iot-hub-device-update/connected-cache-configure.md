---
title: 为 Device Update for Azure IoT 中心配置 Microsoft 联网缓存 | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Device Update for Azure IoT 中心的 Microsoft 联网缓存概述
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811819"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>为 Device Update for Azure IoT 中心配置 Microsoft 联网缓存

Microsoft 联网缓存已部署到 Azure IoT Edge 网关作为 Azure IoT Edge 模块。 与其他 Azure IoT Edge 模块一样，MCC 模块部署环境变量和容器创建选项用于配置 Microsoft 联网缓存模块。  本部分定义了客户成功部署 Microsoft 联网缓存模块以供 Device Update for Azure IoT 中心使用的环境变量和容器创建选项。

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft 联网缓存 Azure IoT Edge 模块部署详细信息

管理员应自行为 Microsoft 联网缓存模块命名。 其他模块或服务交互的通信不会依赖于模块名称。 此外，Microsoft 联网缓存服务器的父子关系也不依赖于此模块名称，而是依赖于如前文所述进行配置的 Azure IoT Edge 网关的 FQDN 或 IP 地址。

Microsoft 联网缓存 Azure IoT Edge 模块环境变量用于将基本模块标识信息和功能模块设置传递到容器。

| 变量名称                 | 值格式                           | 必需/可选 | 功能                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure 订阅 ID GUID             | 必需          | 这是客户密钥，为传递优化服务提供安全的<br>缓存节点身份验证<br>服务。<br>需要此项以使模块正常运行。 |
| CACHE_NODE_ID                 | 缓存节点 ID GUID                     | 必需          | 为传递优化服务唯一标识 Microsoft 联网缓存<br>节点。<br>需要此项以使<br> 模块正常运行。 |
| CUSTOMER_KEY                  | 客户密钥 GUID                     | 必需          | 这是客户密钥，为传递优化服务提供安全的<br>缓存节点身份验证。<br>需要此项以使模块正常运行。|
| STORAGE_ *N* _SIZE_GB           | 其中，N 是缓存驱动器   | 必需          | 指定最多 9 个驱动器来缓存内容，并指定<br>为每个缓存驱动器上的内容分配的最大空间（以 GB 为单位）。 示例：<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>驱动器数必须与<br>在容器创建选项 MicrosoftConnectedCacheN 值中指定的缓存驱动器绑定值匹配<br>缓存的最小大小为 10GB。|
| UPSTREAM_HOST                 | FQDN/IP                                | 可选          | 此值可以指定在<br>联网缓存节点与 Internet 断开连接时<br> 充当代理的上游 Microsoft 联网缓存节点。 此设置用于支持<br> 嵌套 IoT 方案。<br>注意：Microsoft 联网缓存侦听 http 默认端口 80。|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | 可选          | 出站 Internet 代理。<br>如果使用 ISA 95 网络，也可以是 OT DMZ 代理。 |
| CACHEABLE_CUSTOM_N_HOST     | HOST/IP<br>FQDN                        | 可选          | 需要此项以支持自定义包存储库。<br>可以在本地或在 Internet 上托管存储库。<br>可配置的自定义主机数不受限制。<br><br>示例：<br>Name = CACHEABLE_CUSTOM_1_HOST Value = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST Value = packages.bar.com    |
| CACHEABLE_CUSTOM_N_CANONICAL| Alias                                  | 可选          | 需要此项以支持自定义包存储库。<br>此值可用作别名，并将由缓存服务器用于引用<br>其他 DNS 名称。 例如，存储库内容主机名可以为 packages.foo.com，<br>但对于不同的区域，可能会向主机名中添加额外的前缀<br>如 westuscdn.packages.foo.com 和 eastuscdn.packages.foo.com。<br>通过设置规范别名，可以确保来自同一主机但不同 CDN 源的<br>内容不重复。<br>规范值的格式并不重要，但它对于主机必须是唯一的。<br>这可能是将值设置为与主机值匹配的最简单的方法。<br><br>基于以上自定义主机示例的示例：<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL Value = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | 是或否                          | 可选          | 允许在本地网络或 Internet 上查看摘要报告。<br>如果设置为“是”，则需要使用 API 密钥（稍后讨论）以查看摘要报告。 |
| IS_SUMMARY_ACCESS_UNRESTRICTED| 是或否                          | 可选          | 允许在本地网络或 Internet 上查看摘要报告，而无需<br>使用网络中任何设备的 API 密钥。 如果你不想将访问权限<br>限定为通过浏览器查看缓存服务器的摘要数据，请使用此项。 |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft 联网缓存 Azure IoT Edge 模块容器创建选项

用于 MCC 模块部署的容器创建选项提供了与 MCC 模块使用的存储和端口相关的设置控制。 这是用于部署 MCC 的必需容器创建变量的列表。

### <a name="container-to-host-os-drive-mappings"></a>容器与主机操作系统驱动器的映射

需要此项以将容器存储位置映射到磁盘上的存储位置。最多可以指定 9 个位置。

>[!Note]
>驱动器数必须与环境变量 STORAGE_N_SIZE_GB 值 ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/``` 中指定的缓存驱动器绑定值匹配

### <a name="container-to-host-tcp-port-mappings"></a>容器与主机 TCP 端口的映射

此选项指定 MCC 在其上侦听内容请求的外部计算机 http 端口。 默认的 HostPort 为端口 80，目前不支持其他端口，因为 ADU 客户端会在端口 80 上发出请求。 TCP 端口 8081 是 MCC 侦听的内部容器端口，且无法更改。

### <a name="container-service-tcp-port-mappings"></a>容器服务 TCP 端口映射

Microsoft 联网缓存模块包含一个 .NET Core 服务，缓存引擎使用该服务实现各种功能。

>[!Note]
>若要支持 Azure IoT 嵌套边缘，HostPort 不得设置为 5000，因为注册表代理模块已在侦听主机端口 5000。


示例“容器创建选项”

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
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

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft 联网缓存摘要报告

摘要报告当前是供客户查看部署到 Azure IoT Edge 网关的 Microsoft 联网缓存实例的缓存数据的唯一方法。 报告以 15 秒的间隔生成，并包括该时间段的平均统计信息以及模块生存期的聚合统计信息。 客户关注的关键统计信息如下：

* hitBytes - 这是直接从缓存传递的字节数之和。
* missBytes - 这是 Microsoft 联网缓存必须从 CDN 下载以查看缓存而传递的字节数之和。
* eggressBytes - 这是 HitBytes 和 missBytes 的总和，是传递到客户端的总字节数。
* hitRatioBytes - 这是 HitBytes 与 egressBytes 的比值。  例如，如果在一段时间内传递的 eggressBytes 总和等于 hitBytes，则此值为 1。


摘要报告位于 `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary`。将 \<Azure IoT Edge Gateway IP\> 替换为自己 IoT Edge 网关的 IP 地址或主机名。 （请参阅环境变量详细信息，了解此报表的可见性）。