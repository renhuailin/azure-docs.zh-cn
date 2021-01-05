---
title: 在 Azure Stack Edge Pro 设备上概述和部署 GPU Vm
description: 介绍如何使用模板在 Azure Stack Edge Pro 设备上创建和管理 (Vm) 的 GPU 虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 0a7f54d991d31594a6ab522dbf9e73958cde3023
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900138"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 设备的 GPU Vm

本文概述了 Azure Stack Edge Pro 设备上 (Vm) 的 GPU 虚拟机。 本文介绍如何创建 GPU VM，然后安装 GPU 驱动程序扩展以安装适当的 Nvidia 驱动程序。 使用 Azure 资源管理器模板创建 GPU VM，并安装 GPU 驱动程序扩展。 

本文适用于 Azure Stack Edge Pro GPU 和 Azure Stack Edge Pro R 设备。

## <a name="about-gpu-vms"></a>关于 GPU Vm

Azure Stack Edge Pro 设备配备了1或2个 Nvidia 的 Tesla T4 GPU。 若要在这些设备上部署 GPU 加速 VM 工作负荷，请使用 GPU 优化 VM 大小。 例如，应使用 NC T4 v3 系列来部署具有 T4 Gpu 的推理工作负荷。 

有关详细信息，请参阅 [NC T4 v3 系列 vm](../virtual-machines/nct4-v3-series.md)。

## <a name="supported-os-and-gpu-drivers"></a>支持的 OS 和 GPU 驱动程序 

若要利用 Azure N 系列 Vm 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。 

Nvidia GPU 驱动程序扩展安装适当的 Nvidia CUDA 或网格驱动程序。 可以使用 Azure 资源管理器模板安装或管理扩展。

### <a name="supported-os-for-gpu-extension-for-windows"></a>适用于 Windows 的 GPU 扩展支持的 OS

此扩展插件支持 (OSs) 的以下操作系统。 其他版本可能可行，但尚未在 Azure Stack Edge Pro 设备上运行的 GPU Vm 内部进行测试。

| 分发 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>适用于 Linux 的 GPU 扩展支持的 OS

此扩展插件支持以下 OS 发行版，具体取决于特定 OS 版本的驱动程序支持。 其他版本可能可行，但尚未在 Azure Stack Edge Pro 设备上运行的 GPU Vm 内部进行测试。


| 分发 | 版本 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU Vm 和 Kubernetes

在设备上部署 GPU Vm 之前，如果在设备上配置了 Kubernetes，请查看以下注意事项。

#### <a name="for-1-gpu-device"></a>对于1个 GPU 设备： 

- 在 **设备上创建一个 GPU vm，并在设备上创建 Kubernetes 配置**：在此方案中，gpu vm 创建和 Kubernetes 配置都将成功。 在这种情况下，Kubernetes 将无法访问 GPU。

- 在 **设备上配置 Kubernetes，然后创建 GPU VM**：在这种情况下，Kubernetes 将在设备上声明 GPU，而 VM 创建将失败，因为没有可用的 GPU 资源。

#### <a name="for-2-gpu-device"></a>对于2个 GPU 设备

- 在 **设备上创建一个 GPU vm，并在设备上创建 Kubernetes 配置**：在此方案中，你创建的 gpu vm 将在你的设备上声明一个 Gpu，Kubernetes 配置也会成功，并且会声明剩余的一个 gpu。 

- 在 **设备上创建两个 Gpu vm，并在设备上创建 Kubernetes 配置**：在此方案中，两个 gpu vm 将在设备上声明两个 gpu，并成功配置 Kubernetes，且不使用 gpu。 

- 在 **设备上配置 Kubernetes，然后创建 GPU VM**：在这种情况下，Kubernetes 将在设备上声明 gpu，而 VM 创建将失败，因为没有可用的 GPU 资源。

如果在设备上运行 GPU Vm，并同时配置了 Kubernetes，则在 (使用 Stop-AzureRmVM 或 Get-azurermvm) 停止或删除 VM 时，无论何时释放 VM，都有一个 Kubernetes 群集声明设备上可用的所有 Gpu 的风险。 在这种情况下，你将无法重新启动在你的设备上部署的 GPU Vm 或创建 GPU Vm。


## <a name="create-gpu-vms"></a>创建 GPU Vm

在设备上部署 GPU Vm 时，请执行以下步骤：

1. 确定你的设备是否也将运行 Kubernetes。 如果设备将运行 Kubernetes，则需要先创建 GPU VM，然后配置 Kubernetes。 如果首先配置了 Kubernetes，则它将声明所有可用的 GPU 资源并使 GPU VM 创建失败。

1. 将[VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的目录中。

1. 若要创建 GPU Vm，请 [使用模板在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 中的所有步骤，但以下差异除外： 

    1. 配置计算网络时，为计算启用连接到 Internet 的端口。 这样，你就可以下载 gpu Vm 所需的 gpu 驱动程序。

        下面是一个示例，其中端口2已连接到 internet，并已用于启用计算网络。 如果已确定在前面的步骤中不需要 Kubernetes，可以跳过 Kubernetes 节点 IP 和外部服务 IP 分配。

        ![在连接到 internet 的端口上启用计算设置](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. 使用模板创建 VM。 指定 GPU VM 大小时，请确保使用中的 NCasT4 系列， `CreateVM.parameters.json` 因为 Gpu vm 支持这些系列。 有关详细信息，请参阅 [GPU Vm 支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. 成功创建 GPU VM 后，你可以在 Azure 门户的 Azure Stack Edge 资源中的虚拟机列表中查看此 VM。

        ![Azure 门户中的虚拟机列表中的 GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. 选择 VM 并向下钻取详细信息。 复制分配给 VM 的 IP。

    ![分配到 Azure 门户中的 GPU VM 的 IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. 创建 VM 后，使用扩展模板部署 GPU 扩展。 对于 linux Vm，请参阅安装适用于 Linux 和 Windows Vm 的 [gpu 扩展](#gpu-extension-for-linux) ，请参阅安装适用于 [windows 的 gpu 扩展](#gpu-extension-for-windows)。

1. 若要验证 GPU 扩展安装，请连接到 GPU VM：
    1. 如果使用 Windows VM，请遵循 [连接到 WINDOWS vm](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm)中的步骤。 [验证安装](#verify-windows-driver-installation)。
    1. 如果使用 Linux VM，请遵循 [连接到 LINUX vm](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)中的步骤。 [验证安装](#verify-linux-driver-installation)。

1. 如果需要，可以将计算网络切换回你需要的任何内容。 


> [!NOTE]
> 将设备软件版本从2012更新到更高版本时，需要手动停止 GPU Vm。


## <a name="install-gpu-extension"></a>安装 GPU 扩展

根据 VM 的操作系统，可以安装适用于 Windows 或 Linux 的 GPU 扩展。

> [!NOTE]
> 在安装 GPU 扩展之前，请确保设备上为计算网络启用的端口已连接到 Internet 并且具有访问权限。 通过 internet 访问下载 GPU 驱动程序。

### <a name="gpu-extension-for-windows"></a>适用于 Windows 的 GPU 扩展

若要为现有 VM 部署 Nvidia GPU 驱动程序，请编辑 `addGPUExtWindowsVM.parameters.json` 参数文件，然后部署模板 `addGPUextensiontoVM.json` 。

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

下面是本文中使用的示例参数文件：

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

部署模板 `addGPUextensiontoVM.json` 。 此模板将扩展部署到现有 VM。 运行以下命令：

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 扩展部署是一个长时间运行的作业，完成时间大约需要10分钟。

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

若要检查给定 VM 的扩展部署状态，请运行以下命令： 

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

扩展执行输出将记录到以下文件。 请参阅此文件 `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` 以跟踪安装状态。 


如果安装成功，则由 `message` as `Enable Extension` 和表示 `status` `success` 。

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>验证 Windows 驱动程序安装

登录到 VM 并运行随驱动程序一起安装的 nvidia-smi-s 命令行实用程序。 `nvidia-smi.exe`位于 `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` 。 如果看不到该文件，则可能是驱动程序安装仍在后台运行。 等待10分钟，然后重新检查。

如果安装了驱动程序，将看到类似于以下示例的输出： 

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

有关详细信息，请参阅 [适用于 Windows 的 NVIDIA GPU 驱动程序扩展](../virtual-machines/extensions/hpccompute-gpu-windows.md)。

### <a name="gpu-extension-for-linux"></a>适用于 Linux 的 GPU 扩展

若要为现有 VM 部署 Nvidia GPU 驱动程序，请编辑参数文件，然后部署模板 `addGPUextensiontoVM.json` 。  (RHEL) 提供了 Ubuntu 和 Red Hat Enterprise Linux 的特定参数，如以下部分中所述。

#### <a name="edit-parameters-file"></a>编辑参数文件

如果使用 Ubuntu，此 `addGPUExtLinuxVM.parameters.json` 文件将使用以下参数：

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
如果使用 Red Hat Enterprise Linux (RHEL) ，则该 `addGPUExtensionRHELVM.parameters.json` 文件采用以下参数：

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


下面是本文中使用的示例 Ubuntu 参数文件：

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

部署模板 `addGPUextensiontoVM.json` 。 此模板将扩展部署到现有 VM。 运行以下命令：

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 扩展部署是一个长时间运行的作业，完成时间大约需要10分钟。

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
    
模板部署是长时间运行的作业。 若要检查给定 VM 的扩展部署状态，请打开另一个 PowerShell 会话 (以管理员身份运行) 。 运行以下命令： 

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
> 部署完成后，将 `ProvisioningState` 更改为 `Succeeded` 。

扩展执行输出将记录到以下文件中： `/var/log/azure/nvidia-vmext-status` 。

#### <a name="verify-linux-driver-installation"></a>验证 Linux 驱动程序安装

请按照以下步骤验证驱动程序安装：

1. 连接到 GPU VM。 按照 [连接到 LINUX VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm)中的说明进行操作。 

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
2. 运行随驱动程序一起安装的 nvidia-smi-s 命令行实用程序。 如果已成功安装该驱动程序，你将能够运行该实用工具并查看以下输出：

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

有关详细信息，请参阅 [适用于 Linux 的 NVIDIA GPU 驱动程序扩展](../virtual-machines/extensions/hpccompute-gpu-linux.md)。

## <a name="remove-gpu-extension"></a>删除 GPU 扩展

若要删除 GPU 扩展，请使用以下命令：

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

[Azure 资源管理器 cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)