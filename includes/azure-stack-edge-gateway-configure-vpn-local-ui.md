---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466107"
---
在设备的本地 web UI 中执行以下步骤。 此步骤大约要花费15分钟，其中包含 VPN 配置文件 (或服务标记文件) 上传。 

1. **> VPN** 中转到 "配置"。 选择“配置” 。

    ![配置本地 UI 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. 在 " **配置 VPN** " 边栏选项卡中：

    - 启用 **VPN 设置**。
    - 提供 **VPN 共享机密**。 这是你在创建 Azure VPN 连接资源时提供的共享密钥。
    - 提供 **VPN 网关 IP** 地址。 这是 Azure 本地网络网关 IP 地址。
    - 对于 **PFS 组**，请选择 " **无**"。 
    - 对于 " **DH 组**"，选择 " **Group2**"。
    - 对于 **IPsec 完整性方法**，请选择 **SHA256**。
    - 对于 **IPsec 密码转换常量**，请选择 **GCMAES256**。
    - 对于 **IPsec 身份验证转换常量**，请选择 **GCMAES256**。
    - 对于 " **IKE 加密方法**"，请选择 **AES256**。
    - 选择“应用”。 

        ![配置本地 UI 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    有关支持的加密算法的详细信息，请参阅 [关于加密要求和 AZURE VPN 网关](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)。 

3. 若要上传 VPN 路由配置文件，请选择 " **上传**"。 

    ![配置本地 UI 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - 浏览到你在上一步中在本地系统上下载的服务标记 *json* 文件。
    - 选择 "区域" 作为与设备、虚拟网络和网关关联的 Azure 区域。
    - 选择“应用”。 

        ![配置本地 UI 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    在设备上上传大约需7-8 分钟。

4. 要添加特定于客户端的路由，请仅使用 VPN 配置要访问的 IP 地址范围。 

    - 在 " **仅使用 VPN 访问的 IP 地址范围**" 下，选择 " **配置**"。
    - 提供有效的 IPv4 范围，然后选择 " **添加**"。 重复上述步骤以添加其他范围。
    - 选择“应用”。 

        ![配置本地 UI 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

