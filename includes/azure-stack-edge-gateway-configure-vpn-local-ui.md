---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466107"
---
在设备的本地 Web UI 中，执行以下步骤。 此步骤大约需要 15 分钟，其中包括上传 VPN 配置文件（或服务标记文件）。 

1. 转到“配置”>“VPN”。 选择“配置” 。

    ![配置本地 UI 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. 在“配置 VPN”边栏选项卡中：

    - 启用“VPN 设置”。
    - 提供 VPN 共享机密。 这是你在创建 Azure VPN 连接资源时提供的共享密钥。
    - 提供 VPN 网关 IP 地址。 这是 Azure 本地网络网关 IP 地址。
    - 对于“PFS 组”，选择“无” 。 
    - 对于“DH 组”，选择“组 2” 。
    - 对于“IPsec 完整性方法”，选择 SHA256 。
    - 对于“IPsec 密码转换常量”，选择 GCMAES256 。
    - 对于“IPsec 身份验证转换常量”，选择 GCMAES256 。
    - 对于“IKE 加密方法”，选择 AES256 。
    - 选择“应用”。

        ![配置本地 UI 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    有关支持的加密算法的详细信息，请参阅[关于加密要求和 Azure VPN 网关](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)。 

3. 若要上传 VPN 路由配置文件，请选择“上传”。 

    ![配置本地 UI 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - 浏览上一步骤中在本地系统上下载的服务标记 json 文件。
    - 选择该区域作为与设备、虚拟网络和网关关联的 Azure 区域。
    - 选择“应用”。

        ![配置本地 UI 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    在设备上上传大约需 7-8 分钟。

4. 若要添加特定于客户端的路由，请配置 IP 地址范围，使其仅可通过 VPN 访问。 

    - 在“仅使用 VPN 访问的 IP 地址范围”下，选择“配置” 。
    - 提供有效的 IPv4 范围，然后选择“添加”。 重复上述步骤以添加其他范围。
    - 选择“应用”。

        ![配置本地 UI 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

