---
title: Azure Stack Edge Pro GPU、Pro R、Mini R 证书概述
description: 概述可在 Azure Stack Edge Pro GPU 设备上使用的所有证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/01/2021
ms.author: alkohli
ms.openlocfilehash: e082ae9343ff935ceeda168573be9648c6cee631
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358846"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 上有哪些证书？

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍可在 Azure Stack Edge Pro GPU 设备上安装的证书类型。 本文还详细介绍了每种证书类型。  

## <a name="about-certificates"></a>关于证书

证书在公钥和实体（例如域名）之间提供链接，由受信任的第三方（如证书颁发机构）签署（验证）。  证书提供了一种方便的方法来分发受信任的公共加密密钥。 因此，证书确保通信受到信任，并确保你正在向正确的服务器发送加密信息。 

## <a name="deploying-certificates-on-device"></a>在设备上部署证书

在 Azure Stack Edge 设备上，可以使用自签名证书，也可以自带证书。

- 设备生成的证书：最初配置设备时，将自动生成自签名证书。 如果需要，可以通过本地 Web UI 重新生成这些证书。 重新生成证书后，在用于访问设备的客户端上下载并导入证书。

- 自带证书：你也可以选择自带证书。 如果你打算自带证书，则需要遵循一些准则。

    - 首先了解能够与本文中的 Azure Stack Edge 设备一起使用的证书类型。
    - 接下来，查看[每种类型的证书的证书要求](azure-stack-edge-gpu-certificate-requirements.md)。  
    - 然后，可以[通过 Azure PowerShell 创建证书](azure-stack-edge-gpu-create-certificates-powershell.md)或[通过就绪检查器工具创建证书](azure-stack-edge-gpu-create-certificates-tool.md)。
    - 最后，[将证书转换成正确的格式](azure-stack-edge-gpu-prepare-certificates-device-upload.md)，这样它们就可以上传到你的设备。
    - 在设备上[上传证书](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device)。
    - [在访问设备的客户端上导入证书](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)。

## <a name="types-of-certificates"></a>证书类型

以下是你可以为设备引入的各种类型的证书： 
- 签名证书
    - 根 CA
    - 中级

- 节点证书

- 终结点证书   
   
    - Azure 资源管理器证书
    - Blob 存储证书

- 本地 UI 证书
- IoT 设备证书
    
- Kubernetes 证书

    - Edge 容器注册表证书
    - Kubernetes 仪表板证书
    
- Wi-Fi 证书
- VPN 证书  

- 加密证书
    - 支持会话证书

以下部分详细介绍了这些证书。

## <a name="signing-chain-certificates"></a>签名链证书

这些是签署证书的机构或签署证书颁发机构的证书。 

### <a name="types"></a>类型

这些证书可以是根证书或中间证书。 根证书始终是自签名（或自行签名）。 中间证书不是自签名证书，由签署机构签名。

#### <a name="caveats"></a>注意事项

- 根证书应为签名链证书。
- 可以按以下格式在设备上上传根证书： 
    - DER – 它们作为 `.cer` 文件扩展名提供。
    - Base-64 编码 – 它们作为 `.cer` 文件扩展名提供。
    - P7b – 此格式仅用于包含根证书和中间证书的签名链证书。
- 在上传任何其他证书之前，将始终上传签名链证书。


## <a name="node-certificates"></a>节点证书

<!--Your  device could be a 1-node device or a 4-node device.--> 你的设备中的所有节点都将持续相互通信，因此需要建立信任关系。 节点证书提供了建立这种信任的方法。 当你通过 https 使用远程 PowerShell 会话连接到设备节点时，节点证书也会发挥作用。

#### <a name="caveats"></a>注意事项

- 节点证书应以 `.pfx` 格式提供，并带有可以导出的私钥。 
- 你可以创建并上传 1 个通配符节点证书或 4 个单独的节点证书。 
- 如果 DNS 域发生更改，但设备名称未更改，则必须更改节点证书。 如果要自带节点证书，则无法更改设备序列号，只能更改域名。
- 使用下表指导你创建节点证书。
   
    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |节点|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>终结点证书

对于设备公开的任何终结点，都需要证书才能进行可信通信。 终结点证书包括通过 REST API 访问 Azure 资源管理器和 blob 存储时所需的证书。 

如果自带签名证书，则还需要证书的相应签名链。 对于签名链、Azure 资源管理器和设备上的 blob 证书，你将需要在客户端计算机上使用相应的证书，以便对设备进行身份验证和与之通信。

#### <a name="caveats"></a>注意事项

- 终结点证书的格式必须为 `.pfx`，并带有私钥。 签名链应为 DER 格式（`.cer` 文件扩展名）。 
- 自带终结点证书时，这些证书可以是单独的证书或多域证书。 
- 如果要引入签名链，则必须先上传签名链证书，然后才能上传终结点证书。
- 如果设备名称或 DNS 域名发生变更，则必须更改这些证书。
- 可以使用通配符终结点证书。
- 终结点证书的属性类似于典型的 SSL 证书属性。 
- 创建终结点证书时使用下表：

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |两个终结点的多个 SAN 单一证书|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>本地 UI 证书

可以通过浏览器访问设备的本地 Web UI。 若要确保此通信安全，可以上传自己的证书。 

#### <a name="caveats"></a>注意事项

- 本地 UI 证书也以 `.pfx` 格式上传，包含可以导出的私钥。
- 上传本地 UI 证书后，你将需要重新启动浏览器并清除缓存。 请参阅浏览器的特定说明。

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |本地 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge 设备证书

你的设备也是一台 IoT 设备，其计算由连接到它的 IoT Edge 设备启用。 还可上传 IoT Edge 证书，以便在此 IoT Edge 设备和可能与其进行连接的下游设备之间进行任何安全通信。 

此设备具有自签名证书，如果你只想在这台设备上使用计算场景，则可以使用该证书。 但是，如果设备已连接到下游设备，则需要自带证书。

要启用此信任关系，需要安装三个 IoT Edge 证书：

- 根证书颁发机构或所有者证书颁发机构
- 设备证书颁发机构 
- 设备密钥证书


#### <a name="caveats"></a>注意事项

- IoT Edge 证书以 `.pem` 格式上传。 

有关 IoT Edge 证书的更多信息，请查看 [Azure IoT Edge 证书详细信息](../iot-edge/iot-edge-certs.md#iot-edge-certificates)和[创建 IoT Edge 生产证书](/azure/iot-edge/how-to-manage-device-certificates?view=iotedge-2020-11&preserve-view=true#create-production-certificates)。

## <a name="kubernetes-certificates"></a>Kubernetes 证书

以下 Kubernetes 证书可用于 Azure Stack Edge 设备。

- **Edge 容器注册表证书**：如果设备具有 Edge 容器注册表，则需要一个 Edge 容器注册表证书，用于与访问设备上注册表的客户端进行安全通信。
- **仪表板终结点证书**：需要仪表板终结点证书才能访问设备上的 Kubernetes 仪表板。


#### <a name="caveats"></a>注意事项

- Edge 容器注册表证书应： 
    - 是 PEM 格式的证书。
    - 包含以下类型的使用者可选名称 (SAN) 或 CName (CN)：`*.<endpoint suffix>` 或 `ecr.<endpoint suffix>`。 例如：`*.dbe-1d6phq2.microsoftdatabox.com OR ecr.dbe-1d6phq2.microsoftdatabox.com`


- 仪表板证书应：
    - 是 PEM 格式的证书。
    - 包含以下类型的使用者可选名称 (SAN) 或 CName (CN)：`*.<endpoint-suffix>` 或 `kubernetes-dashboard.<endpoint-suffix>`。 例如 `*.dbe-1d6phq2.microsoftdatabox.com` 或 `kubernetes-dashboard.dbe-1d6phq2.microsoftdatabox.com`。 


## <a name="vpn-certificates"></a>VPN 证书

如果在设备上配置 VPN（点到站点），可以自带 VPN 证书，以确保通信是可信的。 根证书安装在 Azure VPN 网关上，客户端证书安装在使用点到站点方式连接到虚拟网络的每个客户端计算机上。

#### <a name="caveats"></a>注意事项

- 必须以 *.pfx* 格式上传包含私钥的 VPN 证书。
- VPN 证书不依赖于设备名称、设备序列号和设备配置。 VPN 证书只需要外部 FQDN。
- 确保设置了客户端 OID。

有关更多信息，请[使用 PowerShell 为点到站点连接生成和导出证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell)。

## <a name="wi-fi-certificates"></a>Wi-Fi 证书

如果设备配置为在 WPA2-Enterprise 无线网络上运行，则还需要一个 Wi-Fi 证书，以用于无线网络上进行的任何通信。 

#### <a name="caveats"></a>注意事项

- 必须以 *.pfx* 格式上传包含私钥的 Wi-Fi 证书。
- 确保设置了客户端 OID。

## <a name="support-session-certificates"></a>支持会话证书

如果你的设备遇到任何问题，则在解决这些问题时，可以在设备上打开远程 PowerShell 支持会话。 若要在此支持会话中启用安全加密通信，可以上传证书。

#### <a name="caveats"></a>注意事项

- 请确保在使用解密工具的客户端计算机上安装了具有私钥的相应 `.pfx` 证书。
- 验证证书的“密钥用法”字段不是“证书签名”。 若要对此进行验证，请右键单击该证书，选择“打开”，然后在“详细信息”选项卡中查找“密钥用法”。 

- 支持会话证书必须以 DER 格式提供，并带有 `.cer` 扩展名。


## <a name="next-steps"></a>后续步骤

[审阅证书要求](azure-stack-edge-gpu-certificate-requirements.md)。
