---
title: 收集 Azure Stack Edge Pro GPU 上的 VM 来宾日志
description: 介绍如何为 Azure Stack Edge Pro GPU 设备上的 VM 创建包含来宾日志的支持包。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 1c25ea8c35b81169119b0f10025b36319d4dc2c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744616"
---
# <a name="collect-vm-guest-logs-on-an-azure-stack-edge-pro-gpu-device"></a>收集 Azure Stack Edge Pro GPU 设备上的 VM 来宾日志

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

若要诊断 Azure Stack Edge Pro GPU 设备上的任何 VM 预配失败，请查看故障虚拟机的来宾日志。 本文介绍如何收集支持包中的 VM 来宾日志。

> [!NOTE]
> 你还可以在 Azure 门户中监视虚拟机的活动日志。 有关详细信息，请参阅[在设备上监视 VM 活动](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。


## <a name="collect-vm-guest-logs-in-support-package"></a>在支持包中收集 VM 来宾日志

若要为 Azure Stack Edge Pro GPU 设备上的故障虚拟机收集来宾日志，请执行以下步骤：

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

2. 通过运行以下命令，收集故障 VM 的来宾内日志，并将这些日志包括到支持包中：

   ```powershell
   Get-VMInGuestLogs -FailedVM
   Get-HcsNodeSupportPackage -Path “\\<network path>” -Include InGuestVMLogFiles -Credential “domain_name\user”
   ```

   可在 `hcslogs\VmGuestLogs` 文件夹中找到这些日志。

3. 若要获取 VM 预配历史记录详细信息，请查看以下日志：

   Linux VM：
   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent.log

   Windows VM：
   - C:\Windows\Azure\Panther\WaSetup.xml

## <a name="next-steps"></a>后续步骤

- [监视 VM 活动日志](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [排查 Azure Stack Edge Pro GPU 上的 VM 预配问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)。