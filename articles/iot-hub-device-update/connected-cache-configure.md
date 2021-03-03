---
title: 为 Azure IoT 中心配置适用于设备更新的 Microsoft 连接缓存 |Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 适用于 Azure IoT 中心的设备更新的 Microsoft 连接缓存概述
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662053"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>为 Azure IoT 中心的设备更新配置 Microsoft 连接缓存

Microsoft 连接缓存部署到作为 Azure IoT Edge 模块的 Azure IoT Edge 网关。 与其他 Azure IoT Edge 模块一样，MCC 模块部署环境变量和容器 create 选项用于配置 Microsoft 连接缓存模块。  本部分定义了客户成功部署 Microsoft 连接缓存模块以供 Azure IoT 中心的设备更新使用的环境变量和容器创建选项。

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Microsoft 连接缓存 Azure IoT Edge 模块部署详细信息

管理员应自行为 Microsoft 连接缓存模块命名。 没有其他模块或服务交互依赖于模块的名称进行通信。 此外，Microsoft 连接缓存服务器的父子关系不依赖于此模块名称，而是配置为前面讨论过的 Azure IoT Edge 网关的 FQDN 或 Ip 地址。

Microsoft 连接缓存 Azure IoT Edge 模块环境变量用于将基本模块标识信息和功能模块设置传递到容器。

| 变量名称                 | 值格式                           | 必需/可选 | 功能                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure 订阅 ID GUID             | 必选          | 这是客户的密钥，它提供安全的<br>用于传递优化的缓存节点的身份验证<br>服务。 要使模块正常运行，需要此项。 |
| CACHE_NODE_ID                 | 缓存节点 ID GUID                     | 必选          | 唯一标识 Microsoft 连接缓存<br>传递优化服务的节点。 需要顺序<br> 适用于 module 的函数。 |
| CUSTOMER_KEY                  | 客户密钥 GUID                     | 必选          | 这是客户的密钥，它提供安全的<br>向传递优化服务提供缓存节点的身份验证。<br>要使模块正常运行，需要此项。|
| STORAGE_ *N* _SIZE_GB           | 其中，N 是所需的 GB 数   | 必选          | 指定最多9个驱动器来缓存内容，并指定<br>为每个缓存驱动器上的内容分配的最大空间（以 Gb 为单位）。 示例：<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>驱动器号必须与指定的缓存驱动器绑定值匹配<br>在容器创建选项中，MicrosoftConnectedCache *N* 值|
| UPSTREAM_HOST                 | FQDN/IP                                | 可选          | 此值可以指定已连接的上游 Microsoft<br>连接缓存节点时充当代理的缓存节点<br> 与 internet 断开连接。 此设置用于支持<br> 嵌套 IoT 方案。<br>**注意：** Microsoft 连接缓存在 http 默认端口80上进行侦听。|
| UPSTREAM_PROXY                | FQDN/IP：端口                           | 可选          | 出站 internet 代理。<br>如果是 ISA 95 网络，也可以是 OT 的 DMZ 代理。 |
| CACHEABLE_CUSTOM_ *N* _HOST     | 主机/IP<br>FQDN                        | 可选          | 需要支持自定义包存储库。<br>可以在本地或在 internet 上托管存储库。<br>对于可配置的自定义主机数没有限制。<br><br>示例：<br>名称 = CACHEABLE_CUSTOM_1_HOST 值 = packages.foo.com<br> 名称 = CACHEABLE_CUSTOM_2_HOST 值 = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | 可选          | 需要支持自定义包存储库。<br>此值可用作别名，并将由缓存服务器用于引用<br>不同的 DNS 名称。 例如，存储库内容主机名可能是 packages.foo.com，<br>但对于不同的区域，可能会有一个添加到主机名的附加前缀<br>如 westuscdn.packages.foo.com 和 eastuscdn.packages.foo.com。<br>通过设置规范别名，可以确保内容不会重复<br>对于来自同一主机但不同 CDN 源的内容。<br>规范值的格式并不重要，但它对于主机必须是唯一的。<br>最简单的方法是将值设置为与主机值匹配。<br><br>基于以上自定义主机示例的示例：<br>名称 = CACHEABLE_CUSTOM_1_CANONICAL 值 = foopackages<br> 名称 = CACHEABLE_CUSTOM_2_CANONICAL 值 = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | 是或否                          | 可选          | 允许在本地网络或 internet 上查看摘要报告。<br>使用稍后讨论 (API 密钥) 如果设置为 true，则需要查看汇总报表。 |
| IS_SUMMARY_ACCESS_UNRESTRICTED| 是或否                          | 可选          | 允许在本地网络或 internet 上查看摘要报表，无需<br>在网络中的任何设备上使用 API 密钥。 如果你不想锁定访问权限，请使用<br>通过浏览器查看缓存服务器的摘要数据。 |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Microsoft 连接缓存 Azure IoT Edge 模块容器创建选项

用于 MCC 模块部署的容器创建选项提供了与 MCC 模块使用的存储和端口相关的设置的控制。 这是用于部署 MCC 的必需容器创建变量的列表。

### <a name="container-to-host-os-drive-mappings"></a>用于主机操作系统驱动器映射的容器

需要将容器存储位置映射到磁盘上的存储位置 <。最多可以指定9个位置。

>[!Note]
>驱动器号必须与环境变量中指定的缓存驱动器绑定值匹配 STORAGE_ *N* _SIZE_GB 值， ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>用于承载 TCP 端口映射的容器

此选项指定 MCC 为内容请求侦听的外部计算机 http 端口。 默认的 HostPort 为端口80，目前不支持其他端口，因为 ADU 客户端会在端口80上发出请求。 TCP 端口8081是 MCC 侦听且无法更改的内部容器端口。

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>容器服务 TCP 端口映射

Microsoft 连接缓存模块包含一个 .NET Core 服务，该服务由缓存引擎用于各种函数。

>[!Note]
>若要支持 Azure IoT 嵌套边缘，不能将 HostPort 设置为5000，因为注册表代理模块已在侦听主机端口5000。

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft 连接缓存摘要报告

汇总报表是当前为客户查看部署到 Azure IoT Edge 网关的 Microsoft 连接缓存实例的缓存数据的唯一方法。 报表按15秒的间隔生成，并包括该时间段的平均统计信息以及该模块生存期的聚合统计信息。 客户将感兴趣的关键统计信息如下：

* **hitBytes** -这是直接从缓存传递的字节数之和。
* **missBytes** -这是为了查看缓存，已传递的 Microsoft 连接缓存必须从 CDN 下载的字节数之和。
* **eggressBytes** -这是 HitBytes 和 missBytes 的总和，是传递给客户端的总字节数。
* **hitRatioBytes** -这是 HitBytes 与 egressBytes 的比值。  如果在一段时间内提供100% 的 eggressBytes 等于 hitBytes，则为1。

此摘要报表位于 `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (参阅下面的环境变量详细信息，详细了解此报表) 。
