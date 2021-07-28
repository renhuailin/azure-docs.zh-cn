---
title: 排查在 Azure Stack Edge Pro GPU 中上传虚拟机映像的问题 | Microsoft Docs
description: 介绍如何排查在 Azure Stack Edge Pro GPU 中上传、下载或删除虚拟机映像时出现的问题。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 3ec5cfb952e666832fe887f65672e228004423bc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482199"
---
# <a name="troubleshoot-virtual-machine-image-uploads-in-azure-stack-edge-pro-gpu"></a>排查在 Azure Stack Edge Pro GPU 中上传虚拟机映像的问题

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查在 Azure Stack Edge Pro GPU 设备上下载和管理虚拟机 (VM) 映像时出现的问题。


## <a name="unable-to-add-vm-image-to-blob-container"></a>无法将 VM 映像添加到 Blob 容器

错误说明：在 Azure 门户中，尝试将 VM 映像上传到 Blob 容器时，“添加”按钮不可用，无法上传该映像。 当你对设备的资源组或订阅没有所需的“参与者”角色权限时，“添加”按钮不可用。

建议的解决方案：确保你具有向资源组或存储帐户添加文件所需的参与者权限。 有关详细信息，请参阅 [Azure Stack Edge 资源的先决条件](azure-stack-edge-deploy-prep.md#prerequisites)。


## <a name="invalid-blob-type-for-the-source-blob-uri"></a>源 Blob URI 的 Blob 类型无效

错误说明：无法下载以块 Blob 形式存储的 VHD。 VHD 必须以页 Blob 的形式存储才能供下载。

建议的解决方案：将 VHD 以页 Blob 的形式上传到 Azure 存储帐户。 然后下载该 Blob。 有关上传说明，请参阅[使用存储资源管理器进行上传](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)。


## <a name="only-blobs-formatted-as-vhds-can-be-imported"></a>只能导入格式为 VHD 的 Blob

错误说明：无法导入 VHD，因为它不符合格式要求。 虚拟硬盘必须是固定大小的第 1 代 VHD 才能导入。

建议的解决方案： 

- 按照[从 Windows VHD 准备通用映像以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) 中的步骤，从源 VHD 或 VHDX 为第 1 代虚拟机创建固定大小的 VHD。

- 如果你首选使用 PowerShell，则：

   - 可以在 Hyper-V 的 Windows PowerShell 模块中使用 [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps&preserve-view=true)。 不能使用 Convert-VHD 将 VM 映像从第 2 代 VM 转换为第 1 代 VM，而只能使用[从 Windows VHD 准备通用映像以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) 中的门户过程。
  
   - 如果需要查明当前的 VHD 类型，请使用 [Get-VHD](/powershell/module/hyper-v/get-vhd?view=windowsserver2019-ps&preserve-view=true)。


## <a name="the-condition-specified-using-http-conditional-headers-is-not-met"></a>不满足使用 HTTP 条件标头指定的条件

错误说明：如果在尝试从 Azure 下载 VHD 时对其进行了任何更改，下载会失败，因为 Azure 中的 VHD 与正在下载的 VHD 不匹配。 如果尝试下载 VHD，而此时将 VHD 上传到 Azure 的操作尚未完成，则也会发生此错误。

建议的解决方案：等待 VHD 上传完成，不对 VHD 进行任何更改。 然后，再次尝试下载该 VHD。


## <a name="next-steps"></a>后续步骤

* 了解如何[通过 Azure 门户部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
* 了解如何[通过 Azure PowerShell 部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。
