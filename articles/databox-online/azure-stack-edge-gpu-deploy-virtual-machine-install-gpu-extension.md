---
title: 在 Azure Stack Edge Pro GPU 设备上的 GPU VM 上安装 GPU 扩展
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上的 GPU 虚拟机 (VM) 上安装 GPU 扩展。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 80337c4953182e903655eddb360fc8b49a88ca59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732928"
---
# <a name="install-gpu-extension-on-vms-for-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备的 VM 上安装 GPU 扩展

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文介绍如何安装 GPU 驱动程序扩展，以在 Azure Stack Edge 设备上运行的 GPU VM 上安装适当的 Nvidia 驱动程序。 本文介绍在 Windows 和 Linux VM 上使用 Azure 资源管理器模板安装 GPU 扩展的安装步骤。

> [!NOTE]
> 在 Azure 门户中，可以在 VM 创建期间或部署 VM 之后安装 GPU 扩展。 有关步骤和要求，请参阅[部署 GPU 虚拟机](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)。


## <a name="prerequisites"></a>先决条件

在设备上运行的 GPU VM 上安装 GPU 扩展之前，请确保：

1. 你有权访问在其上部署了一个或多个 GPU VM 的 Azure Stack Edge 设备。 请参阅如何[在设备上部署 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)。

    - 请确保设备上为计算网络启用的端口已连接到 Internet 并具有访问权限。 GPU 驱动程序是通过 Internet 访问下载的。

        在下面的示例中，端口 2 已连接到 Internet 并用于启用计算网络。 如果未在环境中部署 Kubernetes，可以跳过 Kubernetes 节点 IP 和外部服务 IP 分配。

        ![Azure Stack Edge 设备的“计算”窗格屏幕截图。 突出显示了端口 2 的计算设置。](media/azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension/enable-compute-network-1.png)
1. [将 GPU 扩展模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的那个目录中。
1. 验证用于访问设备的客户端是否仍通过 Azure PowerShell 连接到 Azure 资源管理器。 与 Azure 资源管理器的连接每 1.5 小时或每当 Azure Stack Edge 设备重启时过期一次。 如果发生这种情况，所执行的任何 cmdlet 都将返回错误消息，表示不再连接到 Azure。 需重新登录。 有关详细说明，请参阅[连接到 Azure Stack Edge 设备上的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。

## <a name="edit-parameters-file"></a>编辑参数文件

根据 VM 的操作系统，可以安装适用于 Windows 或 Linux 的 GPU 扩展。


### <a name="windows"></a>[Windows](#tab/windows)

要为现有 VM 部署 Nvidia GPU 驱动程序，请编辑 `addGPUExtWindowsVM.parameters.json` 参数文件，然后部署模板 `addGPUextensiontoVM.json`。

文件 `addGPUExtWindowsVM.parameters.json` 采用以下参数：

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

### <a name="linux"></a>[Linux](#tab/linux)

要为现有 Linux VM 部署 Nvidia GPU 驱动程序，请编辑参数文件，然后部署模板 `addGPUextensiontoVM.json`。 

如果使用的是 Ubuntu 或 Red Hat Enterprise Linux (RHEL)，`addGPUExtLinuxVM.parameters.json` 文件将采用以下参数：

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    }
    }
    }
```

下面是本文中使用的一个 Ubuntu 参数文件示例：

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
        }
    }
}
```

### <a name="gpu-vms-from-rhel-byos-images"></a>RHEL BYOS 映像中的 GPU VM

如果使用 Red Hat Enterprise Linux 自带订阅映像 (RHEL BYOS) 创建了 VM，请确保：

- 已按照[使用 RHEL BYOS 映像](azure-stack-edge-gpu-create-virtual-machine-image.md)中的步骤进行操作。 
- 创建 GPU VM 后，在 Red Hat 客户门户中注册并订阅 VM。 如果 VM 未正确注册，则不会继续进行安装，因为 VM 未获得授权。 请参阅[使用 Red Hat 订阅管理器一步注册并自动订阅](https://access.redhat.com/solutions/253273)。 此步骤允许安装脚本下载 GPU 驱动程序的相关包。
- 可以手动安装 `vulkan-filesystem` 包或将 CentOS7 存储库添加到 yum 存储库列表。 安装 GPU 扩展时，安装脚本将查找 CentOS7 存储库上的 `vulkan-filesystem` 包（适用于 RHEL7）。 

---

## <a name="deploy-template"></a>部署模板 

### <a name="windows"></a>[Windows](#tab/windows)

部署模板 `addGPUextensiontoVM.json`。 此模板将扩展部署到现有 VM。 运行下面的命令：

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 扩展部署是一个长时间运行的作业，大约需要 10 分钟才能完成。

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

### <a name="linux"></a>[Linux](#tab/linux)

部署模板 `addGPUextensiontoVM.json`。 此模板将扩展部署到现有 VM。 运行下面的命令：

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 扩展部署是一个长时间运行的作业，大约需要 10 分钟才能完成。

下面是示例输出：

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
---

## <a name="track-deployment"></a>跟踪部署

### <a name="windows"></a>[Windows](#tab/windows)

要查看给定 VM 扩展的部署状态，请运行以下命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
下面是示例输出：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

扩展执行输出将记录到以下文件。 请参考文件 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` 以跟踪安装状态。 


如果 `message` 为 `Enable Extension` 且 `status` 为 `success`，表示安装成功。

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

### <a name="linux"></a>[Linux](#tab/linux)

模板部署是一个长时间运行的作业。 要检查给定 VM 扩展的部署状态，请打开另一个 PowerShell 会话（以管理员身份运行）。 运行下面的命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
下面是示例输出： 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 部署完成后，`ProvisioningState` 将变为 `Succeeded`。

扩展执行输出将记录到以下文件：`/var/log/azure/nvidia-vmext-status`。

---

## <a name="verify-driver-installation"></a>验证驱动程序安装

### <a name="windows"></a>[Windows](#tab/windows)

登录到 VM 并运行随驱动程序安装的 nvidia-smi 命令行实用工具。 `nvidia-smi.exe` 位于 `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe`。 如果你没有看到此文件，可能是因为驱动程序安装仍在后台运行。 请等待 10 分钟，然后再次查看。

如果驱动程序安装成功，你会看到类似以下示例的输出： 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

有关详细信息，请参阅[适用于 Windows 的 Nvidia GPU 驱动程序扩展](../virtual-machines/extensions/hpccompute-gpu-windows.md)。

> [!NOTE]
> 安装完 GPU 驱动程序和 GPU 扩展后，不再需要使用具有 Internet 访问权限的端口进行计算。

### <a name="linux"></a>[Linux](#tab/linux)

请按照以下步骤操作，验证驱动程序是否安装成功：

1. 连接到 GPU VM。 按照[连接到 Linux VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm) 中的说明操作。 

    下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. 运行随驱动程序安装的 nvidia-smi 命令行实用工具。 如果驱动程序安装成功，你将能够运行此实用工具并看到以下输出：

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

有关详细信息，请参阅[适用于 Linux 的 Nvidia GPU 驱动程序扩展](../virtual-machines/extensions/hpccompute-gpu-linux.md)。

> [!NOTE]
> 安装完 GPU 驱动程序和 GPU 扩展后，不再需要使用具有 Internet 访问权限的端口进行计算。


---


## <a name="remove-gpu-extension"></a>删除 GPU 扩展

要删除 GPU 扩展，请使用以下命令：

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

下面是示例输出：

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```


## <a name="next-steps"></a>后续步骤

了解如何：

- [排查 GPU 扩展问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)。
- [监视设备上的 VM 活动](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [管理 VM 磁盘](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md)。
- [管理 VM 网络接口](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)。
- [管理 VM 大小](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md)。