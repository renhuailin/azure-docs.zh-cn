---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517544"
---
1. 在开始之前，请确保：

    1. 已在 Azure 中使用 Azure Stack Edge 资源配置并[激活 Azure Stack Edge Pro 设备](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md)。
    1. [已在 Azure 门户中的此设备上配置了计算](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute)。
    
1. [连接到 PowerShell 接口](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 使用以下命令在设备上启用 MPS。

    ```powershell
    Start-HcsGpuMPS
    ```