---
title: 教程：在 Azure 门户中配置 Azure Stack Edge Pro R 设备的证书 | Microsoft Docs
description: Azure Stack Edge Pro R 部署教程会指导你在物理设备上配置证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: abea192f209b968c6842e985620c067c93990687
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548142"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>教程：为 Azure Stack Edge Pro R 配置证书

本教程介绍如何通过本地 Web UI 为 Azure Stack Edge Pro R 设备配置证书。

此步骤所花费的时间取决于你选择的特定选项，以及在环境中建立证书流的方式。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书
> * 配置 VPN
> * 配置静态加密

## <a name="prerequisites"></a>先决条件

配置和设置 Azure Stack Edge Pro R 设备之前，请确保：

* 已按照[安装 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) 中详述的内容安装物理设备。
* 如果你计划使用自己的证书：
    - 应该使用适当的格式来准备证书，包括签名链证书。 有关证书的详细信息，请参阅[管理证书](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>为设备配置证书

1. 在“证书”页中，将配置证书。 根据在“设备”页中更改的是设备名称还是 DNS 域，可以为证书选择以下某个选项。

    - 如果在上一步中没有更改设备名称或 DNS 域，则可以跳过此步骤，继续进行下一步。 设备首先已自动生成自签名证书。 

    - 如果更改了设备名称或 DNS 域，会看到证书的状态显示为“无效”。 

        ![本地 Web UI“证书”页 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        选择证书查看状态详细信息。

        ![本地 Web UI“证书”页 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        这是因为证书不反映更新的设备名称和 DNS 域（在使用者名称和使用者备用名称中使用）。 若要成功激活设备，可使用自己的已签名终结点证书和相应的签名链。 先添加签名链，然后上传终结点证书。 有关详细信息，请转到[在 Azure Stack Edge Pro R 设备上使用自己的证书](#bring-your-own-certificates)。

    - 如果更改了设备名称或 DNS 域，但没有使用你自己的证书，那么激活操作将遭到阻止。

    
#### <a name="bring-your-own-certificates"></a>使用自己的证书

请按照以下步骤添加自己的证书，包括签名链。

1. 若要上传证书，请在“证书”页上选择“+ 添加证书” 。

    ![本地 Web UI“证书”页 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. 首先上传签名链，然后选择“验证和添加”。

    ![本地 Web UI“证书”页 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. 现在，可以上传其他证书。 例如，可以上传 Azure 资源管理器和 Blob 存储终结点证书。

    ![本地 Web UI“证书”页 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    还可以上传本地 Web UI 证书。 上传该证书后，需要启动浏览器并清除缓存。 然后需要连接到设备本地 Web UI。  

    ![本地 Web UI“证书”页 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    还可以上传节点证书。

    ![本地 Web UI“证书”页 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    最后，你可上传 VPN 证书。

    ![本地 Web UI“证书”页 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    可以随时选择证书并查看详细信息，以确保这些内容与上传的证书匹配。

    证书页面应会更新，显示新添加的证书。

    ![本地 Web UI“证书”页 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > 除 Azure 公有云外，需要在激活所有云配置（Azure 政府或 Azure Stack）之前引入签名链证书。

1. 选择“< 返回到‘开始使用’”。

## <a name="configure-vpn"></a>配置 VPN

1. 在“安全”磁贴上，为 VPN 选择“配置” 。

    ![本地 Web UI“VPN”页面](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    若要配置 VPN，需要先确保你已在 VPN 上完成所有必要的配置。 有关详细信息，请参阅[配置先决条件](azure-stack-edge-placeholder.md)和[配置 VPN 的 Azure 资源](azure-stack-edge-placeholder.md)。 完成此操作后，就可在本地 UI 中进行配置。
    
    1. 在 VPN 页面上，选择“配置”。
    2. 在“配置 VPN”边栏选项卡中：

    - 启用“VPN 设置”。
    - 提供 VPN 共享机密。 这是你在创建 Azure VPN 连接对象时提供的共享密钥。
    - 提供 VPN 网关 IP 地址。 这是 Azure 本地网络网关 IP 地址。
    - 对于“PFS 组”，选择“无” 。 
    - 对于“DH 组”，选择“组 2” 。
    - 对于“IPsec 完整性方法”，选择 SHA256 。
    - 对于“IPseccipher 转换常量”，选择 GCMAES256 。
    - 对于“IPsec 身份验证转换常量”，选择 GCMAES256 。
    - 对于“IKE 加密方法”，选择 AES256 。
    - 选择“应用”。

        ![配置本地 UI 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. 若要上传 VPN 路由配置文件，请选择“上传”。 
    
        ![配置本地 UI 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - 浏览上一步骤中在本地系统上下载的 VPN 配置 json 文件。
        - 选择该区域作为与设备、虚拟网络和网关关联的 Azure 区域。
        - 选择“应用”。
    
            ![配置本地 UI 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. 若要添加特定于客户端的路由，请配置 IP 地址范围，使其仅可通过 VPN 访问。 
    
        - 在“仅使用 VPN 访问的 IP 地址范围”下，选择“配置” 。
        - 提供有效的 IPv4 范围，然后选择“添加”。 重复上述步骤以添加其他范围。
        - 选择“应用”。
    
            ![配置本地 UI 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. 选择“< 返回到‘开始使用’”。

## <a name="configure-encryption-at-rest"></a>配置静态加密

1. 在“安全”磁贴上，为静态加密选择“配置” 。 此设置是必需项；在成功配置该设置后，你才可激活设备。 

    出厂时，在设备创建映像后，会启用卷级别的 BitLocker 加密。 你在收到设备后，需要配置静态加密。 这会重新创建存储池和卷，你可提供 BitLocker 密钥来启用静态加密，进而为静态数据创建第二层加密。

1. 在“静态加密”窗格中，输入 32 个字符、Base-64 编码的密钥。 这是一次性配置，此密钥用于保护实际的加密密钥。 你可选择自动生成此密钥或输入一个密钥。

    ![本地 Web UI“静态加密”窗格](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    激活设备后，密钥将保存在“云详细信息”页上的密钥文件中。

1. 选择“应用”。 此操作耗时数分钟，操作状态显示在“安全”磁贴上。

    ![本地 Web UI“静态加密”页面](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. 在状态显示为“已完成”后，选择“< 返回到‘开始使用’” 。

现可激活设备。 


## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书
> * 配置 VPN
> * 配置静态加密

若要了解如何激活 Azure Stack Edge Pro R 设备，请参阅：

> [!div class="nextstepaction"]
> [激活 Azure Stack Edge Pro R 设备](./azure-stack-edge-pro-r-deploy-activate.md)
