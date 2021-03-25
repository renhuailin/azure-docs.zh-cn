---
title: 教程：在 Azure 门户中加密和激活 Azure Stack Edge Mini R 设备 | Microsoft Docs
description: 本教程介绍 Azure Stack Edge Mini R 部署方法，指导你加密和激活物理设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: e5076888e692ad16e89f30d455e7f8c4e22a41b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96463826"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>教程：激活 Azure Stack Edge Mini R

本教程介绍如何通过本地 Web UI 激活 Azure Stack Edge Mini R 设备。

完成激活过程需要大约 15 分钟。

本教程介绍了以下内容：

> [!div class="checklist"]
> * 先决条件
> * 激活物理设备

## <a name="prerequisites"></a>先决条件

配置和设置 Azure Stack Edge Mini R 设备之前，请确保：

* 对于物理设备： 
    
    - 已根据[安装 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md) 中详述的内容安装了物理设备。
    - 已按[配置网络、计算网络和 Web 代理](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)中详述的内容配置网络和计算网络设置
    - 如果通过“设备”页面更改了设备名称或 DNS 域，则应上传自己的设备证书。 若要详细了解这些步骤，请参阅[配置证书、VPN 和静态加密](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)。 如果尚未执行此步骤，将阻止激活。
    - 你已为设备配置了静态加密。 如果尚未执行此步骤，则会在设备激活过程中出现错误，并阻止激活。 有关详细信息，请转到[配置证书、VPN 和静态加密](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)。
    
* 已从所创建的用于管理 Azure Stack Edge Mini R 设备的 Azure Stack Edge 服务获得激活密钥。 有关详细信息，请转到[准备部署 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)。


## <a name="activate-the-device"></a>激活设备

1. 在设备的本地 Web UI 中，转到“开始使用”页。
2. 在“激活”磁贴中选择“激活” 。 

    ![本地 Web UI“云详细信息”第 1 页](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. 在“激活”窗格中：
    1. 输入你在[为 Azure Stack Edge Pro R 获取激活密钥](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)中获得的激活密钥。

    1. 可以启用主动日志收集，以允许 Microsoft 根据设备的运行状况收集日志。 通过这种方式收集的日志将上传到 Azure 存储帐户。
    
    1. 选择“应用”。 

    ![本地 Web UI“云详细信息”页 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. 首先激活设备。 随后系统会提示你下载密钥文件。
    
    ![本地 Web UI“云详细信息”页 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
    选择“下载并继续”，并将 device-serial-no.json 文件保存在设备之外的安全位置。 该密钥文件包含设备上的 OS 磁盘和数据磁盘的恢复密钥。 可能需要这些密钥来辅助将来的系统恢复。

    下面是 json 文件的内容：

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    下表对各密钥进行了说明：
    
    |字段  |说明  |
    |---------|---------|
    |`Id`    | 这是设备的 ID。        |
    |`DataVolumeBitLockerExternalKeys`|这些是数据磁盘的 BitLockers 密钥，用于恢复设备上的本地数据。|
    |`SystemVolumeBitLockerRecoveryKey`| 这是系统卷的 BitLocker 密钥。 此密钥有助于恢复设备的系统配置和系统数据。 |
    |`SEDEncryptionExternalKey`| 用户提供的或系统生成的此密钥用于保护具有内置加密的自加密数据驱动器。 |
    |`ServiceEncryptionKey`| 此密钥可保护经过 Azure 服务的数据流。 此密钥确保 Azure 服务的安全威胁不会导致存储的信息受到安全威胁。 |

6. 转至“概述”页面。 设备状态应显示为“已激活”。

    ![本地 Web UI“云详细信息”页 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
已完成设备激活。 现在可以在设备上添加共享。

如果在激活期间遇到任何问题，请转到[对激活和 Azure Key Vault 错误进行故障排除](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors)。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 先决条件
> * 激活物理设备

若要了解如何使用 Azure Stack Edge Mini R 设备传输数据，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge Mini R 传输数据](./azure-stack-edge-j-series-deploy-add-shares.md)
