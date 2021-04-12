---
title: 教程：在 Azure 门户中配置 Azure Stack Edge Mini R 设备的证书 | Microsoft Docs
description: Azure Stack Edge Mini R 部署教程会指导你在物理设备上配置证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062992"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>教程：配置证书, VPN, 对 Azure Stack Edge Mini R 加密

本教程介绍如何通过本地 Web UI 为 Azure Stack Edge Mini R 设备配置证书、VPN 和静态加密。

此步骤所花费的时间取决于你选择的特定选项，以及在环境中建立证书流的方式。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书
> * 配置 VPN
> * 配置静态加密

## <a name="prerequisites"></a>先决条件

配置和设置 Azure Stack Edge Mini R 设备之前，请确保：

* 已根据[安装 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md) 中详述的内容安装了物理设备。

* 如果你计划使用自己的证书：
    - 应该使用适当的格式来准备证书，包括签名链证书。 有关证书的详细信息，请参阅[管理证书](azure-stack-edge-gpu-manage-certificates.md)

    - 如果你的设备已在 Azure 政府、Azure 政府机密或 Azure 政府最高机密云中部署，但未在 Azure 公有云中部署，则需具备签名链证书，然后才能激活该设备。 
    有关证书的详细信息，请参阅[管理证书](azure-stack-edge-gpu-manage-certificates.md)。


## <a name="configure-certificates-for-device"></a>为设备配置证书

1. 在“证书”页中，将配置证书。 根据在“设备”页中更改的是设备名称还是 DNS 域，可以为证书选择以下某个选项。

    - 如果你未在上一步中更改默认设备名称或默认 DNS 域，而且不想使用自己的证书，那么你可跳过此步骤，继续到下一步。 设备首先已自动生成自签名证书。 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - 如果更改了设备名称或 DNS 域，会看到证书的状态显示为“无效”。 

        ![本地 Web UI“证书”页 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        选择证书查看状态详细信息。

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        证书状态为“无效”，原因是证书没有反映出更新后的设备名称和 DNS 域（在使用者名称和使用者备用名称中使用）。 若要成功激活设备，可使用自己的已签名终结点证书和相应的签名链。 先添加签名链，然后上传终结点证书。 有关详细信息，请转到[在 Azure Stack Edge Mini R 设备上使用自己的证书](#bring-your-own-certificates)。


    - 如果更改了设备名称或 DNS 域，但没有使用你自己的证书，那么激活操作将遭到阻止。


#### <a name="bring-your-own-certificates"></a>使用自己的证书

在上一步中，你已在此设备上添加签名链。 现在，你可上传终结点证书、节点证书、本地 UI 证书和 VPN 证书。 请按照以下步骤添加你自己的证书。

1. 若要上传证书，请在“证书”页上选择“+ 添加证书” 。

    [![本地 Web UI“证书”页 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. 你可上传其他证书。 例如，可以上传 Azure 资源管理器和 Blob 存储终结点证书。

    ![本地 Web UI“证书”页 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. 还可以上传本地 Web UI 证书。 上传该证书后，需要启动浏览器并清除缓存。 然后需要连接到设备本地 Web UI。  

    ![本地 Web UI“证书”页 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. 还可以上传节点证书。


    ![本地 Web UI“证书”页 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. 最后，你可上传 VPN 证书。
        
    ![本地 Web UI“证书”页](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. 可以随时选择证书并查看详细信息，以确保这些内容与上传的证书匹配。

    [![本地 Web UI“证书”页 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    证书页面应会更新，显示新添加的证书。

    [![本地 Web UI“证书”页 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > 除 Azure 公有云外，需要在激活所有云配置（Azure 政府或 Azure Stack Hub）之前引入签名链证书。


## <a name="configure-vpn"></a>配置 VPN

1. 在“安全”磁贴上，为 VPN 选择“配置” 。 

    若要配置 VPN，需要先确保你已在 VPN 上完成所有必要的配置。 有关详细信息，请参阅[通过 PowerShell 为 Azure Stack Edge Mini R 设备配置 VPN](azure-stack-edge-placeholder.md)。 完成此操作后，就可在本地 UI 中进行配置。
    
    1. 在 VPN 页面上，选择“配置”。
        ![配置 VPN 本地 UI 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. 在“配置 VPN”边栏选项卡中：

        1. 提供电话簿作为输入内容。
        2. 提供 Azure 数据中心 IP 范围 JSON 文件作为输入内容。 从 [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) 下载此文件。
        3. 选择 eastus 作为区域。
        4. 选择“应用”。

        ![配置 VPN 本地 UI 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. 配置 IP 地址范围，使其仅可通过 VPN 访问。 
    
        - 在“仅使用 VPN 访问的 IP 地址范围”下，选择“配置” 。
        - 为 Azure 虚拟网络输入你选择的 VNET IPv4 范围。
        - 选择“应用”。
    
        ![配置 VPN 本地 UI 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

你的设备现可进行加密。 配置静态加密。


## <a name="enable-encryption"></a>启用加密功能

1. 在“安全”磁贴上，为静态加密选择“配置” 。 此设置是必需项；在成功配置该设置后，你才可激活设备。 

    ![本地 Web UI“静态加密”页面 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    出厂时，在设备创建映像后，会启用卷级别的 BitLocker 加密。 你在收到设备后，需要配置静态加密。 这会重新创建存储池和卷，你可提供 BitLocker 密钥来启用静态加密，进而为静态数据创建第二层加密。

1. 在“静态加密”窗格中，输入 32 个字符（AES-256 位）、Base-64 编码的密钥。 这是一次性配置，此密钥用于保护实际的加密密钥。 

    ![本地 Web UI“静态加密”页面 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    你也可选择自动生成此密钥。

    ![本地 Web UI“静态加密”页面 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 选择“应用”。 此操作耗时数分钟，操作状态显示在“安全”磁贴上。

    ![本地 Web UI“静态加密”页面 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 在状态显示为“已完成”后，返回到“开始使用” 。

现可激活设备。

## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 为物理设备配置证书
> * 配置 VPN
> * 配置静态加密

若要了解如何激活 Azure Stack Edge Mini R 设备，请参阅：

> [!div class="nextstepaction"]
> [激活 Azure Stack Edge Mini R 设备](./azure-stack-edge-mini-r-deploy-activate.md)
