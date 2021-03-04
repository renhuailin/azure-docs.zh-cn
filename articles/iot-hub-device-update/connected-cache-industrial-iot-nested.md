---
title: 适用于工业 IoT 配置的 Azure IoT Edge 中的 Microsoft 联网缓存 | Microsoft Docs
description: 适用于工业 IoT 配置的 Azure IoT Edge 中的 Microsoft 联网缓存教程
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658674"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Microsoft 联网缓存预览部署方案示例：适用于工业 IoT 配置的 Azure IoT Edge 中的 Microsoft 联网缓存

制造网络通常按照 [Purdue 网络模型](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture)（包含在 [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) 和 [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) 标准中）在层次结构层中进行组织。 在这些网络中，只有顶层与云具有连接性，层次结构中的较低层只能与相邻的南北层进行通信。

此 GitHub 示例（[适用于工业 IoT 的 Azure IoT Edge](https://github.com/Azure-Samples/iot-edge-for-iiot)）部署以下内容：

* Azure 中的模拟 Purdue 网络
* 工业资产 
* Azure IoT Edge 网关的层次结构
  
这些组件将用于获取工业数据并安全地将其上传到云，而不会影响网络的安全性。 可以部署 Microsoft 联网缓存，以支持在符合 ISA 95 标准的网络中的所有级别下载内容。

在符合 ISA 95 标准的网络中配置 Microsoft 联网缓存部署的关键是，在 L3 IoT Edge 网关上同时配置 OT 代理和上游主机。

1. 按照两级嵌套 IoT Edge 网关示例中所述，在 L5 和 L4 级别配置 Microsoft 联网缓存部署 
2. L3 IoT Edge 网关上的部署必须指定：
   
   * UPSTREAM_HOST - L4 IoT Edge 网关的 IP/FQDN，这是 L3 Microsoft 联网缓存将请求内容。
   * UPSTREAM_PROXY - OT 代理服务器的 IP/FQDN:PORT。

3. OT 代理必须将 L4 MCC FQDN/IP 地址添加到允许列表。

若要验证 Microsoft 联网缓存是否正常工作，请在托管模块的 IoT Edge 设备或网络上的任何设备的终端中执行以下命令。

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```