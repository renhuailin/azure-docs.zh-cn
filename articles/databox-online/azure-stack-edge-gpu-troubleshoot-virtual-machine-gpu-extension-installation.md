---
title: 排查 Azure Stack Edge Pro GPU 上的 GPU VM 的 GPU 扩展问题
description: 介绍如何排查 Azure Stack Edge Pro GPU 上的 GPU VM 的 GPU 扩展安装问题。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: a2e6996bd2b86da470b364da1349248e26b2fa58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747355"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>排查 Azure Stack Edge Pro GPU 上的 GPU VM 的 GPU 扩展问题

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文提供了指导，以解决导致 GPU VM 上的 GPU 扩展在 Azure Stack Edge Pro GPU 设备上安装失败的最常见问题。

有关安装步骤，请参阅[安装 GPU 扩展](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)。

## <a name="vm-size-is-not-gpu-vm-size"></a>VM 的大小不是 GPU VM 的大小

错误说明：GPU VM 的大小必须为 Standard_NC4as_T4_v3 或 Standard_NC8as_T4_v3 的大小。 如果使用任何其他 VM 的大小，将无法附加 GPU 扩展。

建议的解决方案：用 Standard_NC4as_T4_v3 或 Standard_NC8as_T4_v3 VM 的大小创建一个 VM。 有关详细信息，请参阅 [GPU VM 支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。 有关指定大小的信息，请参阅[创建 GPU VM](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)。


## <a name="image-os-is-not-supported"></a>不支持映像 OS

错误说明：GPU 扩展不支持 VM 映像上安装的操作系统。 

建议的解决方案：准备具有 GPU 扩展支持的操作系统的新 VM 映像。 

* 有关支持的操作系统的列表，请参阅 [GPU VM 支持的 OS 和 GPU 驱动程序](./azure-stack-edge-gpu-overview-gpu-virtual-machines.md#supported-os-and-gpu-drivers)。

* 有关 GPU VM 的映像准备要求，请参阅[创建 GPU VM](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)。


## <a name="extension-parameter-is-incorrect"></a>扩展参数不正确

错误说明：在 Linux VM 上部署 GPU 扩展时使用了不正确的扩展设置。 

建议的解决方案：在部署 GPU 扩展之前编辑参数文件。 有关详细信息，请参阅[安装 GPU 扩展](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)。


## <a name="vm-extension-installation-failed-in-downloading-package"></a>下载包时 VM 扩展安装失败

错误说明：扩展预配在扩展安装期间或启用状态期间失败。

1. 检查来宾日志中的关联错误。 若要收集来宾日志，请参阅[在 Azure Stack Edge Pro 上收集 VM 的来宾日志](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。

   在 Linux VM 上：
   * 在 `/var/log/waagent.log` 或 `/var/log/azure/nvidia-vmext-status` 中查找。

   在 Windows VM 上：
   * 在 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` 中查找错误状态。
   * 查看完整的执行日志：`C:\WindowsAzure\Logs\WaAppAgent.txt`。

   如果在包下载过程中安装失败，则此错误表示 VM 无法访问公用网络来下载驱动程序。

建议的解决方案：

1.  在连接到 Internet 的端口上启用计算。 若要获取指导，请参阅[创建 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)。

1.  在门户中停止 VM，以解除分配 VM。 若要停止 VM，请转到“虚拟机” > “概述”，然后选择 VM。 然后，在 VM 属性页上，选择“停止”。<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  创建新 VM。


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>VM 扩展失败，出现错误 `dpkg is used/yum lock is used` (Linux VM)

错误说明：Linux VM 上的 GPU 扩展部署失败，因为另一个进程正在使用 `dpkg` 或另一个进程创建了 `yum lock`。 

建议的解决方案：若要解决此问题，请执行以下步骤：

1.  若要了解锁的应用进程，请搜索 \var\log\azure\nvidia-vmext-status 日志中的错误，例如“dpkg 由另一个进程使用”或“另一个应用持有 `yum lock`”。

1. 等待进程完成，或结束进程。

1.  再次[安装 GPU 扩展](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)。

1.  如果扩展部署再次失败，请创建新的 VM，并确保在你安装 GPU 扩展之前锁不存在。


## <a name="next-steps"></a>后续步骤

[收集来宾日志并创建支持包](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
