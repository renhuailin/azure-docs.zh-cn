---
title: 通过 Azure 门户在 Azure Stack Edge Pro GPU 上部署 VM
description: 了解如何通过 Azure 门户在 Azure Stack Edge Pro GPU 上创建和管理 VM。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749805"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>通过 Azure 门户在 Azure Stack Edge Pro GPU 设备上部署 VM

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

你可以使用 Azure 门户、模板、Azure PowerShell cmdlet 以及通过 Azure CLI 或 Python 脚本在 Azure Stack Edge Pro GPU 设备上创建和管理虚拟机 (VM)。 本文介绍了如何使用 Azure 门户在 Azure Stack Edge Pro GPU 设备上创建和管理 VM。

> [!IMPORTANT] 
> 我们建议你为管理云设备上部署的 VM 的用户启用多重身份验证。
        
## <a name="vm-deployment-workflow"></a>VM 部署工作流

下面是此部署工作流的基本摘要信息：

1. 在 Azure Stack Edge 设备上启用计算网络接口。 此步骤将在指定的网络接口上创建一个虚拟交换机。
1. 通过 Azure 门户启用 VM 的云管理。
1. 使用 Azure 存储资源管理器将 VHD 上传到 Azure 存储帐户。 
1. 使用上传的 VHD 将 VHD 下载到设备上，然后从 VHD 创建 VM 映像。 
1. 使用在前面的步骤中创建的服务：
    1. 所创建的 VM 映像。
    1. 与已启用计算网络接口关联的虚拟交换机。
    1. 与虚拟交换机关联的子网。

    并创建或指定以下内联资源：
    1. VM 名称，为 VM 选择支持的 VM 大小和登录凭据。 
    1. 创建新的数据磁盘或附加现有的数据磁盘。
    1. 为 VM 配置静态或动态 IP。 如果提供静态 IP，请在已启用计算网络接口的子网范围中选择一个可用 IP。

    使用上述资源创建 VM。

## <a name="prerequisites"></a>先决条件

在开始通过 Azure 门户在设备上创建和管理 VM 之前，请确保：

1. 已按照[步骤 1：配置 Azure Stack Edge Pro GPU 设备](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)中所述，在 Azure Stack Edge Pro GPU 设备上完成了网络设置。

    1. 已启用计算网络接口。 此网络接口 IP 用于为 VM 部署创建虚拟交换机。 在设备的本地 UI 中，转到“计算”。 选择用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    1. 在网络接口上启用计算。 Azure Stack Edge Pro GPU 创建并管理与该网络接口相对应的虚拟交换机。

1. 你有权访问 Windows VHD 或 Linux VHD，你将使用该 VHD 为你打算创建的 VM 创建 VM 映像。

## <a name="deploy-a-vm"></a>部署 VM

按照以下步骤在 Azure Stack Edge Pro GPU 设备上创建 VM。

### <a name="add-a-vm-image"></a>添加 VM 映像

1. 将 VHD 上传到 Azure 存储帐户。 请按照[使用存储资源管理器进行上传](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)中的步骤操作。

   有关准备 VHD 的信息，请参阅[从 Windows VHD 准备通用化映像](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)。

   [排查 VM 映像上传问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)。

1. 在 Azure 门户中，转到设备的 Azure Stack Edge 资源。 然后转到“Edge 服务” > “虚拟机”。 

    ![屏幕截图：显示 Azure Stack Edge 设备“概述”窗格，其中突出显示了“Edge 服务”下的“虚拟机”选项。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. 在“概述”页面上， 选择“启用”以启用虚拟机云管理。 

    ![屏幕截图：Azure Stack Edge 设备虚拟机视图中的“概述”窗格。 突出显示了可启用 VM 云管理的“启用”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 第一步是添加 VM 映像。 在之前的步骤中，你已将 VHD 上传到存储帐户。 你将使用此 VHD 创建 VM 映像。

    选择“+ 添加映像”，从存储帐户下载 VHD，并将其添加到设备中。 下载过程需要几分钟时间，具体取决于 VHD 的大小和用于下载的 Internet 带宽。 

    ![屏幕截图：虚拟机“概述”窗格，其中突出显示了“+ 添加映像”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. 在“添加映像”窗格中，输入以下字段条目。 然后选择“添加”  。

    |字段  |说明  |
    |---------|---------|
    |从存储 blob 下载    |浏览到你上传 VHD 的存储账户中存储 blob 的位置。         |
    |下载到    | 自动设置为将要部署 VM 的当前设备。        |
    |Edge 资源组  |选择要将映像添加到的资源组。 |
    |将映像另存为      | 将要从 VHD（该 VHD 已上传至存储帐户）创建的 VM 映像名称。        |
    |OS 类型     |选择 Windows 或 Linux 作为 VHD 的操作系统，你将使用此 VHD 创建 VM 映像。         |
   

    ![屏幕截图：显示虚拟机“添加映像”页，其中突出显示了“添加”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. 随即将下载 VHD 并创建 VM 映像。 映像创建过程需要几分钟时间才能完成。 你将收到 VM 映像创建成功的通知。<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![屏幕截图：Azure Stack Edge 设备虚拟机“概述”。 在“映像”窗格上，突出显示了新创建的 VM。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. 成功创建的 VM 映像将添加到“映像”窗格的映像列表中。

    ![屏幕截图：显示 Azure Stack Edge 设备虚拟机视图中“映像”窗格。 突出显示了 VM 映像的条目。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    “部署”窗格将更新以指示部署状态。

    ![屏幕截图：显示 Azure Stack Edge 设备虚拟机视图中“部署”窗格。 突出显示了 VM 的条目。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    新添加的映像也将显示在“概述”页中。

    ![屏幕截图：显示 Azure Stack Edge 设备虚拟机“概述”窗格。 突出显示了新添加的 VM 映像。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>添加 VM

创建 VM 映像后，请按照以下步骤创建 VM。

1. 在“概述”页中找到“虚拟机”，然后选择“+ 添加虚拟机”。  

    ![屏幕截图：显示 Azure Stack Edge 设备虚拟机“概述”窗格。 突出显示了“+ 添加虚拟机”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. 在“基本信息”选项卡中，输入以下参数。

    |参数 |说明  |
    |---------|---------|
    |虚拟机名称     | 输入新虚拟机的名称。        |
    |Edge 资源组     | 为与 VM 关联的所有资源创建新的资源组。        |
    |映像     | 从设备上提供的 VM 映像中进行选择。        |
    |大小     | 从[支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md)中进行选择。<br>对于 GPU VM，请选择 [NCasT4-v3 系列中的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)。 |
    |用户名     | 使用管理员的默认用户名“azureuser”登录到 VM。        |
    |身份验证类型    | 可选择 SSH 公钥或用户定义密码。       |
    |SSH 公钥 | 选择 **SSH 公钥** 身份验证类型时显示。 粘贴 SSH 公钥。 |
    |密码     | 选择 **密码** 身份验证类型时显示。 输入密码以登录到 VM。 密码长度必须至少为 12 个字符，且符合定义的[复杂性要求](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)。 |
    |确认密码    | 再次输入密码。        |

    ![屏幕截图：显示 Azure Stack Edge“添加虚拟机”向导中“基本信息”选项卡。 突出显示了“基本信息”选项卡和“下一步：磁盘”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    在完成时选择“下一步:**磁盘”** 。

1. 在“磁盘”选项卡中，将磁盘附加到 VM。 
    
    1. 你可以选择“创建并附加新磁盘”或“附加现有磁盘” 。

        ![屏幕截图：显示 Azure Stack Edge“添加虚拟机”向导中“磁盘”选项卡。 突出显示了“创建并附加新磁盘”选项。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. 选择“创建并附加新磁盘”。 在“创建新磁盘”窗格中，提供磁盘的名称和大小 (GiB)。

        ![屏幕截图：显示 Azure Stack Edge“添加虚拟机”中“创建新磁盘”屏幕。 突出显示了“确定”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. 重复上述过程，添加更多磁盘。 创建的磁盘会显示在“磁盘”选项卡中。选择“下一步：网络”。

1. 在“网络”选项卡中，为 VM 配置网络连接。

    |参数  |说明 |
    |---------|---------|
    |虚拟网络    | 如果在网络接口上启用了计算，请从下拉列表中选择在 Azure Stack Edge 设备上创建的虚拟交换机。    |
    |子网     | 此字段将自动填充与启用了计算的网络接口关联的子网。         |
    |IP 地址     | 为 VM 提供静态或动态 IP。 静态 IP 应该是指定子网范围内可用的空闲 IP。        |

    ![屏幕截图：Azure Stack Edge“添加虚拟机”中的“网络”选项卡。 突出显示了“网络”选项卡和“下一步：高级”按钮。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    选择“下一页:高级”。 在“高级”选项卡上，可以选择在 VM 部署期间安装的扩展，并可以指定用于自定义 VM 的 `cloud-init` 脚本。

1. 如果要创建 VM 时在 VM 上安装扩展，请选择“选择要安装的扩展”。 在“添加扩展”屏幕上，选择该扩展。

    有关在 VM 部署期间安装 GPU 扩展的详细步骤，请参阅[部署 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)。

    ![将 GPU 扩展添加到“添加虚拟机”中“高级”选项卡的 2 个步骤的插图。 突出显示了可选择和添加扩展的选项。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. 如果要使用 `cloud-init` 实用工具在新 VM 首次启动时对其进行自定义，请在“高级”选项卡中将 `cloud-init` 脚本粘贴到“自定义数据和 cloud init”下的“自定义数据”框中。   

    有关使用 `cloud-init` 的详细信息，请参阅 [Cloud-init 概述](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)。

    ![屏幕截图：显示“创建新虚拟机”向导中“高级”选项卡，其中突出显示了“自定义数据”框中的 cloud init 脚本。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    在完成时选择“下一步:查看 + 创建”。

1. 在“查看 + 创建”选项卡中，查看 VM 的规格。 然后选择“创建”。

    ![屏幕截图：显示 Azure Stack Edge“添加虚拟机”向导中“查看 + 创建”选项卡。 突出显示了“查看 + 创建”选项卡。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. 随即开始创建 VM，此过程可能最多需要 20 分钟。 你可以转到“部署”来监视 VM 的创建过程。

    ![屏幕截图：显示 Azure Stack Edge 设备虚拟机视图中“部署”窗格。 突出显示了 VM 的条目。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. 成功创建 VM 后，“概述”窗格上将显示新的 VM。

    ![屏幕截图：显示 Azure Stack Edge 设备的虚拟机“概述”窗格。 列表中突出显示了新的虚拟机。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 选择新创建的 VM，以转到“虚拟机”。

    ![屏幕截图：Azure Stack Edge 设备“虚拟机”窗格。 突出显示了“虚拟机”标签和虚拟机条目。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    选择 VM，查看详细信息。

    ![屏幕截图：显示 Azure Stack Edge 虚拟机“概述”窗格中的“详细信息”选项卡。 突出显示了“概述”标签和“网络中的 IP 地址”。](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    你将使用网络接口的 IP 地址连接到 VM。

## <a name="connect-to-a-vm"></a>连接到 VM

连接步骤可能会有所不同，具体取决于所创建的是 Windows VM 还是 Linux VM。 通过 Azure 门户无法连接到设备上部署的 VM。 请按照以下步骤连接到 Linux VM 或 Windows VM。

### <a name="connect-to-a-linux-vm"></a>连接到 Linux VM

按照以下步骤连接到 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>连接到 Windows VM

按照以下步骤连接到 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>后续步骤

- [部署 GPU VM](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [排查 VM 部署问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [监视设备上的 VM 活动](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [监视 VM 上的 CPU 和内存](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

