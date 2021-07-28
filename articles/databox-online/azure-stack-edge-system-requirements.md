---
title: Azure Stack Edge Pro FPGA 系统需求
description: 了解 Azure Stack Edge Pro FPGA 的软件和网络需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/26/2021
ms.author: alkohli
ms.openlocfilehash: 7be7715586339a4d38370acbe4a047f86b3a917f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460507"
---
# <a name="azure-stack-edge-pro-fpga-system-requirements"></a>Azure Stack Edge Pro FPGA 系统需求

本文介绍 Microsoft Azure Stack Edge Pro FPGA 解决方案以及连接到 Azure Stack Edge Pro FPGA 的客户端的重要系统需求。 建议在部署 Azure Stack Edge Pro FPGA 之前仔细查看这些信息。 在部署和执行后续操作期间，如有必要，可以回来参考此信息。

Azure Stack Edge Pro FPGA 系统需求包括： 

- **主机的软件要求** - 介绍支持的平台、本地配置 UI 的浏览器、SMB 客户端以及访问设备的客户端的任何其他要求。
- **设备的网络要求** - 提供有关物理设备运转的网络要求的信息。

## <a name="supported-os-for-clients-connected-to-device"></a>连接到设备的客户端支持的 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>访问设备的客户端支持的协议

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本地 Web UI 支持的浏览器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>网络端口要求

### <a name="port-requirements-for-azure-stack-edge-pro-fpga"></a>Azure Stack Edge Pro FPGA 的端口需求

下表列出了需要在防火墙中打开以允许 SMB、云或管理流量的端口。 在此表中，*入* 或 *入站* 表示传入客户端请求访问设备的方向。 “出”或“出站”表示 Azure Stack Edge Pro FPGA 设备从外部（超出部署范围）发送数据的方向，例如，向 Internet 发送出站数据。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的端口要求

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地 Edge 设备来与 Azure 云进行出站通信。 仅在 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备（例如，云到设备的消息传送）的特定情况下，才需要进行入站通信。

对托管 Azure IoT Edge 运行时的服务器使用下表中的端口配置：

| 端口号。 | 入或出 | 端口范围 | 必需 | 指南 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 出       | WAN        | 是      | 为 IoT Edge 预配打开此出站端口。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。|

有关完整信息，请转到 [IoT Edge 部署的防火墙和端口配置规则](../iot-edge/troubleshoot.md)。

## <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式

通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 Azure Stack Edge Pro FPGA 设备和服务依赖于其他 Microsoft 应用程序，例如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 这些更改要求网络管理员在需要时为 Azure Stack Edge Pro FPGA 监视和更新防火墙规则。

绝大多数情况下，建议基于 Azure Stack Edge Pro FPGA 固定 IP 地址为出站流量设置防火墙规则。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> - 设备（源）IP 应始终设置为所有已启用云的网络接口。
> - 目标 IP 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>网关功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>计算功能的 URL 模式

| URL 模式                      | 组件或功能                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器注册表（必填）               |
| https://\*.azurecr.io                     | 个人和第三方容器注册表（可选） | 
| https://\*.azure-devices.net              | Iot 中心访问权限（必填）                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure 政府网关的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure 政府计算的 URL 模式

| URL 模式                      | 组件或功能                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft 容器注册表（必填）               |
| https://\*.azure-devices.us              | Iot 中心访问权限（必填）           |
| https://\*.azurecr.us                    | 个人和第三方容器注册表（可选） | 

## <a name="internet-bandwidth"></a>Internet 带宽

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>调整计算规模时的注意事项

在开发和测试解决方案时，请充分利用自己的经验，以确保 Azure Stack Edge Pro FPGA 设备上有足够的容量并获得设备的最佳性能。

应考虑的因素包括：

- 容器详细信息 - 请考虑以下内容。

    - 你的工作负载中有多少个容器？ 你可能有大量轻型容器，而不是少量资源密集型容器。
    - 你向这些容器分配了哪些资源？它们消耗哪些资源？
    - 你的容器共享多少层？
    - 是否有未使用的容器？ 已停止的容器仍会占用磁盘空间。
    - 你的容器使用哪种语言编写？
- 处理的数据大小 - 你的容器将处理多少数据？ 这些数据会消耗磁盘空间还是直接在内存中处理？
- 预期性能 - 你的解决方案所需的性能特征有哪些？ 

要了解和优化解决方案的性能，可以使用：

- Azure 门户中提供的计算指标。 前往 Azure Stack Edge 资源，然后转到“监视”>“指标”。 查看“Edge 计算 - 内存使用”和“Edge 计算 - CPU 百分比”，以了解可用资源以及这些资源的消耗情况 。
- 可通过设备的 PowerShell 接口使用的监视命令，例如：

    - `dkr` stats，用于获取容器资源使用统计信息的实时流。 该命令支持 CPU、内存使用、内存限制和网络 IO 指标。
    - `dkr system df`，用于获取已使用磁盘空间量的信息。 
    - `dkr image [prune]`，用于清理未使用的映像并释放空间。
    - `dkr ps --size`，用于查看正在运行的容器的估计大小。 

    有关可用命令的详细信息，请转到[计算模块的监视和故障排除](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)。

最后，在部署到生产环境之前，请确保在数据集上验证解决方案并量化 Azure Stack Edge Pro FPGA 的性能。


## <a name="next-step"></a>后续步骤

- [部署 Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)