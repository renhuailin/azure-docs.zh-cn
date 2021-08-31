---
title: 在 Azure Stack Edge Pro GPU 设备上部署 GPU VM
description: 介绍如何通过 Azure 门户或使用模板在 Azure Stack Edge Pro GPU 上创建和部署 GPU 虚拟机 (VM)。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740818"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上部署 GPU VM

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文介绍如何在 Azure 门户中或使用 Azure 资源管理器模板创建 GPU VM。

使用 Azure 门户快速部署单个 GPU VM。 可以在创建 VM 期间或之后安装 GPU 扩展。 或者，使用 Azure 资源管理器模板高效地部署和管理多个 GPU VM。

## <a name="create-gpu-vms"></a>创建 GPU VM

可以通过门户或使用 Azure 资源管理器模板部署 GPU VM。

有关 GPU VM 支持的操作系统、驱动程序和 VM 大小的列表，请参阅[什么是 GPU 虚拟机？](azure-stack-edge-gpu-overview-gpu-virtual-machines.md)。 有关部署注意事项，请参阅 [GPU VM 和 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)。


> [!IMPORTANT]
> 如果设备将运行 Kubernetes，请先部署 GPU VM，再配置 Kubernetes。 如果先配置了 Kubernetes，它将声明所有可用的 GPU 资源，而创建 GPU VM 将会失败。 有关 1-GPU 和 2-GPU 设备上的 Kubernetes 部署注意事项，请参阅 [GPU VM 和 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)。

### <a name="portal"></a>[Portal](#tab/portal)

在设备上通过 Azure 门户部署 GPU VM 时，请执行以下步骤：

1. 若要创建 GPU VM，请按照[在 Azure Stack Edge 上使用 Azure 门户部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 中的所有步骤执行操作，并满足以下配置要求：

    - 在“基本”选项卡上，选择 [NCasT4-v3 系列中的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

       ![Azure Stack Edge 中“添加虚拟机”部分“基本”选项卡的屏幕截图。 突出显示了“大小”选项（包括 GPU VM 支持的 VM 大小）。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - 若要在部署期间安装 GPU 扩展，请在“高级”选项卡上选择“选择要安装的扩展”。 然后，选择要安装的 GPU 扩展。 GPU 扩展仅适用于符合 [NCasT4-v3 系列中的虚拟机 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)的虚拟机。
        
        > [!NOTE]
        > 如果使用 Red Hat 映像，则需要在 VM 部署后安装 GPU 扩展。 按照[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)中的步骤执行操作。
    
       ![在“添加虚拟机”的“高级”选项卡中添加 GPU 扩展的步骤插图。 突出显示了选择和添加扩展的选项。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       “高级”选项卡显示已选的扩展。

       ![“添加虚拟机”的“高级”选项卡的屏幕截图。 突出显示了已安装的 GPU 扩展。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. 成功创建 GPU VM 后，可以在 Azure 门户的 Azure Stack Edge 资源中的虚拟机列表中查看此 VM。

    ![Azure Stack Edge 中“虚拟机”视图的屏幕截图，其中突出显示了新创建的 GPU VM。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    选择 VM，并进一步了解详细信息。 确保 GPU 扩展的状态为“已成功”。

    ![Azure Stack Edge“详细信息”窗格的屏幕截图。 突出显示了已安装的扩展（包括已安装的 GPU 扩展）。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[模板](#tab/templates)

使用 Azure 资源管理器模板在设备上部署 GPU VM 时，请执行以下步骤：

1. [将 VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的那个目录中。

1. 在 Azure Stack Edge 设备上部署 VM 之前，必须先将客户端配置为使用 Azure PowerShell 通过 Azure 资源管理器连接到设备。 有关详细说明，请参阅[连接到 Azure Stack Edge 设备上的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。

1. 若要创建 GPU VM，请按照[在 Azure Stack Edge 上使用模板部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 中的所有步骤执行操作，并满足以下配置要求： 
            
    - 指定 GPU VM 大小时，请确保在 `CreateVM.parameters.json` 中使用 NCasT4-v3 系列，因为它们受 GPU VM 支持。 有关详细信息，请参阅 [GPU VM 支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    成功创建 GPU VM 后，可以在 Azure 门户的 Azure Stack Edge 资源中的虚拟机列表中查看此 VM。

    ![Azure Stack Edge 中“虚拟机”视图的屏幕截图。 突出显示了新创建的 GPU VM。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. 选择 VM，并进一步了解详细信息。 复制分配给 VM 的 IP 地址。

    ![Azure Stack Edge 虚拟机“详细信息”窗格的屏幕截图。 突出显示了“网络”下的 IP 地址。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

创建 VM 后，可[使用扩展模板部署 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)。

---

> [!NOTE]
> 将设备软件版本从 2012 更新到更高版本时，需要手动停止 GPU VM。

## <a name="install-gpu-extension-after-deployment"></a>部署后安装 GPU 扩展

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。 在 Azure 门户中，可以在 VM 部署期间或之后安装 GPU 扩展。 如果使用模板，则在创建 VM 后再安装 GPU 扩展。

---

### <a name="portal"></a>[Portal](#tab/portal)

如果在创建 VM 时未安装 GPU 扩展，请按照以下步骤在部署的 VM 上安装该扩展：

1. 转到要将 GPU 扩展添加到的虚拟机。

    ![Azure Stack Edge 设备“虚拟机”视图的屏幕截图，VM 列表中突出显示了一个虚拟机。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. 在“详细信息”中，选择“+ 添加扩展” 。 然后，选择要安装的 GPU 扩展。

    GPU 扩展仅适用于符合 [NCasT4-v3 系列中的虚拟机 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)的虚拟机。 如果需要，可以在[部署后安装 GPU 扩展](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment)。

![该插图显示了使用虚拟机“详细信息”窗格上的“+ 添加扩展”按钮将 GPU 扩展添加到 Azure Stack Edge 设备的 VM 上的 2 个步骤。](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> 无法通过门户删除 GPU 扩展。 请改为使用 Azure PowerShell 中的 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) cmdlet。 有关说明，请参阅[删除 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension)

### <a name="templates"></a>[模板](#tab/templates)

使用模板创建 GPU VM 时，可在部署后安装 GPU 扩展。 有关使用模板在 Windows 虚拟机或 Linux 虚拟机上部署 GPU 扩展的详细步骤，请参阅[安装 GPU 扩展](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)。

---

## <a name="next-steps"></a>后续步骤

- [排查 VM 部署问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [排查 GPU 扩展问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [监视设备上的 VM 活动](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [监视 VM 上的 CPU 和内存](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
