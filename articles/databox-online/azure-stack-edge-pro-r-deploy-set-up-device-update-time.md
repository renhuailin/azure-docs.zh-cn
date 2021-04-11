---
title: 教程：在 Azure 门户中设定 Azure Stack Edge Pro R 设备的时间、设备和更新设置
description: Azure Stack Edge Pro R 部署教程会指导你为物理设备设定设备、更新和时间设置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: 94abfd704d000b907158b2559a268718046b6661
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059609"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-r"></a>教程：为 Azure Stack Edge Pro R 配置设备设置

本教程介绍为 Azure Stack Edge Pro R 设备配置与设备相关的设置。 可以通过本地 Web UI 设置设备名称、更新服务器和时间服务器。

设备设置可能需要大约 5-7 分钟才能完成。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 配置设备设置
> * 配置更新 
> * 配置时间

## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge Pro R 设备上配置与设备相关的设置之前，请确保：

* 对于物理设备：

    - 已按照[安装 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) 中详述的内容安装物理设备。
    - 已根据[教程：配置 Azure Stack Edge Pro R 的网络](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)。


## <a name="configure-device-settings"></a>配置设备设置

按照以下步骤配置设备相关设置：

1. 在“设备”页上执行以下步骤：

    1. 为设备输入一个易记名称。 易记名称必须包含 1 到 13 个字符，可以包含字母、数字和连字符。

    2. 为设备提供 DNS 域。 此域用于将设备设置为文件服务器。

    3. 若要验证并应用已配置的设备设置，请选择“应用”。

        ![本地 Web UI“设备”页 1](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-2.png)

        如果更改了设备名称和 DNS 域，则设备上自动生成的自签名证书将不起作用。 配置证书时，需要选择以下选项之一： 
        
        - 生成并下载设备证书。 
        - 为设备引入你自己的证书，包括签名链。
    

        ![本地 Web UI“设备”页 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-3.png)

    4. 更改设备名称和 DNS 域后，将创建 SMB 终结点。  

    5. 应用设置后，选择“下一步:更新服务器”。

        ![本地 Web UI“设备”页 3](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>配置更新

1. 在“更新”页上，现在可以配置设备更新的下载位置。  

    - 可以直接从“Microsoft 更新服务器”获取更新。

        ![本地 Web UI“更新服务器”页](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-2.png)

        也可以选择从“Windows Server Update Services”(WSUS) 部署更新。 提供 WSUS 服务器的路径。
        
        ![本地 Web UI“更新服务器”页 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > 如果配置了单独的 Windows 更新服务器，并且选择通过 https（而不是 http）进行连接，则需要提供连接到更新服务器所需的签名链证书。 有关如何创建和上传证书的信息，请转到[管理证书](azure-stack-edge-gpu-manage-certificates.md)。         
        > 若要在断开连接模式下工作（例如分层到 Modular Data Center 的 Azure Stack Edge 设备），请启用 WSUS 选项。 在激活期间，设备会扫描是否存在更新。如果未设置服务器，则激活将失败。 


2. 选择“应用”。 
3. 配置更新服务器后，选择“下一步:时间”。
    

## <a name="configure-time"></a>配置时间

按照以下步骤在设备上配置时间设置。 

> [!IMPORTANT]
> 尽管时间设置是可选的，但强烈建议在本地网络上为设备配置主要 NTP 服务器和辅助 NTP 服务器。 如果本地服务器不可用，则可以配置公共 NTP 服务器。

NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。

1. 在“时间”页上，可以为设备选择时区以及主要和辅助 NTP 服务器。  
    
    1. 在“时区”下拉列表中选择时区，使之与部署设备的地理位置相对应。
        设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。

    2. 在“主 NTP 服务器”框中输入设备的主服务器，或者接受默认值：time.windows.com。  
        确保网络允许 NTP 流量从数据中心传递到 Internet。

    3. （可选）在“辅助 NTP 服务器”框中，为设备输入辅助服务器。

    4. 若要验证并应用配置的时间设置，请选择“应用”  。

        ![本地 Web UI“时间”页](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/time-2.png)

2. 应用设置后，选择“下一步:证书”。


## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 配置设备设置
> * 配置更新 
> * 配置时间

若要了解如何为 Azure Stack Edge Pro R 设备配置证书，请参阅：

> [!div class="nextstepaction"]
> [配置证书](./azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)
