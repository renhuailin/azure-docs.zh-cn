---
title: Azure Stack Edge Pro 设备上的 GPU VM 概述和部署
description: 介绍如何使用模板在 Azure Stack Edge Pro 设备上创建和管理 GPU 虚拟机 (VM)。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438176"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 设备上的 GPU VM

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文概述了 Azure Stack Edge Pro 设备上的 GPU 虚拟机 (VM)。 本文介绍了如何创建 GPU VM，然后安装 GPU 驱动程序扩展以安装适当的 Nvidia 驱动程序。 使用 Azure 资源管理器模板创建 GPU VM，并安装 GPU 驱动程序扩展。 

本文适用于 Azure Stack Edge Pro GPU 和 Azure Stack Edge Pro R 设备。

## <a name="about-gpu-vms"></a>关于 GPU VM

Azure Stack Edge Pro 设备配备了 1 个或 2 个 Nvidia 的 Tesla T4 GPU。 要在这些设备上部署 GPU 加速的 VM 工作负载，请使用 GPU 优化的 VM 大小。 例如，应使用 NC T4 v3 系列来部署采用 T4 GPU 的推理工作负载。 

有关详细信息，请参阅 [NC T4 v3 系列 VM](../virtual-machines/nct4-v3-series.md)。

## <a name="supported-os-and-gpu-drivers"></a>支持的 OS 和 GPU 驱动程序 

要利用 Azure N 系列 VM 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。 

Nvidia GPU 驱动程序扩展可以安装适当的 Nvidia CUDA 或 GRID 驱动程序。 你可以使用 Azure 资源管理器模板来安装或管理扩展。

### <a name="supported-os-for-gpu-extension-for-windows"></a>适用于 Windows 的 GPU 扩展支持的 OS

此扩展支持以下操作系统 (OS)。 其他版本可能也有效，但尚未在 Azure Stack Edge Pro 设备上运行的 GPU VM 上对这些版本进行内部测试。

| 分发 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>适用于 Linux 的 GPU 扩展支持的 OS

此扩展支持以下 OS 发行版，具体取决于特定 OS 版本对驱动程序的支持。 其他版本可能也有效，但尚未在 Azure Stack Edge Pro 设备上运行的 GPU VM 上对这些版本进行内部测试。


| 分发 | 版本 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VM 和 Kubernetes

在设备上部署 GPU VM 之前，如果设备上已配置 Kubernetes，请了解以下注意事项。

#### <a name="for-1-gpu-device"></a>对于使用 1 个 GPU 的设备： 

- 在设备上创建 GPU VM，然后配置 Kubernetes：在这种情况下，创建 GPU VM 和配置 Kubernetes 均能成功。 此时 Kubernetes 无法访问 GPU。

- 在设备上配置 Kubernetes，然后创建 GPU VM：在这种情况下，Kubernetes 将回收设备上的 GPU，且创建 VM 将失败，因为没有可用的 GPU 资源。

#### <a name="for-2-gpu-device"></a>对于使用 2 个 GPU 的设备

- 在设备上创建 GPU VM，然后配置 Kubernetes：在这种情况下，你创建的 GPU VM 将回收设备上的一个 GPU，且配置 Kubernetes 将成功，并回收剩余的一个 GPU。 

- 在设备上创建两个GPU VM，然后配置 Kubernetes：在这种情况下，两个 GPU VM 将回收设备上的两个 GPU，并且 Kubernetes 配置成功，没有 GPU。 

- 在设备上配置 Kubernetes，然后创建 GPU VM：在这种情况下，Kubernetes 将回收设备上的两个 GPU，且创建 VM 将失败，因为没有可用的 GPU 资源。

如果你在设备上运行了 GPU VM，并且配置了 Kubernetes，那么每当解除分配 VM 时（使用 Stop-AzureRmVM 或 Remove-AzureRmVM 停止或删除 VM 时），都存在 Kubernetes 群集将回收设备上所有可用的 GPU 的风险。 在这种情况下，你将无法重启设备上部署的 GPU VM，也无法创建 GPU VM。


## <a name="create-gpu-vms"></a>创建 GPU VM

在设备上部署 GPU VM 时，请执行以下步骤：

1. 确定设备是否将同时运行 Kubernetes。 如果设备将运行 Kubernetes，需要先创建 GPU VM，然后再配置 Kubernetes。 如果先配置了 Kubernetes，它将回收所有可用的 GPU 资源，创建 GPU VM 便会失败。

1. [将 VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的那个目录中。

1. 要创建 GPU VM，请按照[使用模板在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 中的所有步骤操作，只不过存在以下差异： 

    1. 配置计算网络时，启用连接到 Internet 的端口，以进行计算。 这样，你就可以下载 GPU VM 的 GPU 扩展所需的 GPU 驱动程序了。

        在下面的示例中，端口 2 已连接到 Internet 并用于启用计算网络。 如果你在前面的步骤中已确定不需要 Kubernetes，可以跳过 Kubernetes 节点 IP 和外部服务 IP 分配。

        ![在连接到 Internet 的端口上启用计算设置](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. 使用模板创建 VM。 指定 GPU VM 大小时，请确保在 `CreateVM.parameters.json` 中使用 NCasT4-v3 系列，因为它们受 GPU VM 支持。 有关详细信息，请参阅 [GPU VM 支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. 成功创建 GPU VM 后，可以在 Azure 门户的 Azure Stack Edge 资源中的虚拟机列表中查看此 VM。

        ![Azure 门户中虚拟机列表中的 GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. 选择 VM 并进一步了解详细信息。 复制分配给 VM 的 IP。

    ![Azure 门户中分配给 GPU VM 的 IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. 创建 VM 后，使用扩展模板部署 GPU 扩展。 对于 linux VM，请参阅[安装适用于 Linux 的 GPU 扩展](#gpu-extension-for-linux)；对于Windows VM，请参阅[安装适用于 Windows 的 GPU 扩展](#gpu-extension-for-windows)。

1. 要验证 GPU 扩展安装，请连接到 GPU VM：
    1. 如果使用的是 Windows VM，请按照[连接到 Windows VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm) 中的步骤操作。 [验证安装](#verify-windows-driver-installation)。
    1. 如果使用的是 Linux VM，请按照[连接到 Linux VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm) 中的步骤操作。 [验证安装](#verify-linux-driver-installation)。

1. 如有需要，可将计算网络切换回你需要的任何位置。 


> [!NOTE]
> 将设备软件版本从 2012 更新到更高版本时，需要手动停止 GPU VM。


## <a name="install-gpu-extension"></a>安装 GPU 扩展

根据 VM 的操作系统，可以安装适用于 Windows 或 Linux 的 GPU 扩展。

> [!NOTE]
> 在安装 GPU 扩展之前，请确保设备上为计算网络启用的端口已连接到 Internet 并具有访问权限。 GPU 驱动程序是通过 Internet 访问下载的。

### <a name="gpu-extension-for-windows"></a>适用于 Windows 的 GPU 扩展

要为现有 VM 部署 Nvidia GPU 驱动程序，请编辑 `addGPUExtWindowsVM.parameters.json` 参数文件，然后部署模板 `addGPUextensiontoVM.json`。

#### <a name="edit-parameters-file"></a>编辑参数文件

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

下面是本文中使用的一个参数文件示例：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>部署模板

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
#### <a name="track-deployment"></a>跟踪部署

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

#### <a name="verify-windows-driver-installation"></a>验证 Windows 驱动程序是否安装成功

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

### <a name="gpu-extension-for-linux"></a>适用于 Linux 的 GPU 扩展

要为现有 VM 部署 Nvidia GPU 驱动程序，请编辑参数文件，然后部署模板 `addGPUextensiontoVM.json`。 Ubuntu 和 Red Hat Enterprise Linux (RHEL) 具有一些特定的参数文件，我们将在下面几节中介绍。

#### <a name="edit-parameters-file"></a>编辑参数文件

如果使用的是 Ubuntu，`addGPUExtLinuxVM.parameters.json` 文件将采用以下参数：

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
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
如果使用的是 Red Hat Enterprise Linux (RHEL)，`addGPUExtensionRHELVM.parameters.json` 文件将采用以下参数：

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
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
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
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
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>部署模板

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

#### <a name="track-deployment-status"></a>跟踪部署状态    
    
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

#### <a name="verify-linux-driver-installation"></a>验证 Linux 驱动程序是否安装成功

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

[Azure 资源管理器 cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)