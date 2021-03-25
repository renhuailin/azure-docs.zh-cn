---
title: 在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1.2
description: 介绍如何在访问 Azure Stack Edge Pro GPU 设备的 Windows 客户端上配置 TLS 1.2。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4a159f7fa384a6899fb3cbb4db3bba9e0ed02d52
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438858"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>在访问 Azure Stack Edge Pro 设备的 Windows 客户端上配置 TLS 1.2

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

如果使用 Windows 客户端访问 Azure Stack Edge Pro 设备，你需要在客户端上配置 TLS 1.2。 本文提供了在 Windows 客户端上配置 TLS 1.2 的相关资源和指南。 

此处提供的指南基于在运行 Windows Server 2016 的客户端上执行的测试。

## <a name="configure-tls-12-for-current-powershell-session"></a>为当前 PowerShell 会话配置 TLS 1.2

执行以下步骤以在客户端上配置 TLS 1.2。

1. 以管理员身份运行 PowerShell。
2. 若要为当前 PowerShell 会话设置 TLS 1.2，请键入以下内容：
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>在客户端上配置 TLS 1.2

如果要为环境设置系统范围的 TLS 1.2，请遵循以下文档中的指南：

- [通用 - 如何启用 TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [如何在客户端上启用 TLS 1.2](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [如何在站点服务器和远程站点系统上启用 TLS 1.2](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL (Schannel SSP) 中的协议](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [密码套件](/windows-server/security/tls/tls-registry-settings#tls-12)：专门[配置 TLS 密码套件顺序](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)。请务必列出你当前的密码套件，并根据以下列表追加任何缺少的密码套件：

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    还可直接编辑注册表设置来添加这些密码套件。

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 如何设置椭圆曲线

    请务必列出你当前的椭圆曲线，并根据以下列表追加任何缺少的椭圆曲线：

    - P-256 
    - P-384

    还可直接编辑注册表设置来添加这些椭圆曲线。
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [将 RSA 密钥交换的最小大小设置为 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)。



## <a name="next-steps"></a>后续步骤

[连接到 Azure 资源管理器](azure-stack-edge-j-series-connect-resource-manager.md)