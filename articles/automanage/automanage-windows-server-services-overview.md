---
title: 适用于 Windows Server 的 Automanage 服务（预览版）
description: Windows Server Azure Edition 中适用于 Windows Server 的 Automanage 服务和功能概述
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 475ea083a6412f38093f601ce34da8775357fa3e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286456"
---
# <a name="automanage-for-windows-server-services-preview"></a>适用于 Windows Server 的 Automanage 服务（预览版）

适用于 Windows Server 的 Automanage 服务为 Windows Server Azure Edition 带来了专属的新功能。  这些功能包括：
- 热补丁
- 基于 QUIC 的 SMB
- 扩展网络

> [!IMPORTANT]
> 适用于 Windows Server 的 Automanage 服务当前提供公开预览版。 需要执行一个选用过程才能使用下面所述的热补丁功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你可在下面的一个或多个 Windows Server Azure Edition 映像中找到适用于 Windows Server 的 Automanage 功能： 

- Windows Server 2019 Datacenter：Azure Edition (Core)
- Windows Server 2022 Datacenter：Azure Edition（桌面体验）
- Windows Server 2022 Datacenter：Azure Edition (Core)

功能因映像而异，请参阅[入门](#getting-started-with-windows-server-azure-edition)以了解更多详细信息。

## <a name="automanage-for-windows-server-capabilities"></a>适用于 Windows Server 的 Automanage 功能

### <a name="hotpatch"></a>热补丁

在以下映像中，热补丁提供公开预览版：

- Windows Server 2019 Datacenter：Azure Edition (Core)
- Windows Server 2022 Datacenter：Azure Edition (Core)

使用热补丁功能，无需重启即可在 VM 上应用安全更新程序。  此外，适用于 Windows Server 的 Automanage 可自动执行热修补的载入、配置和编排。  要了解详细信息，请参阅[热补丁](automanage-hotpatch.md)。  

### <a name="smb-over-quic"></a>基于 QUIC 的 SMB

在以下映像中，基于 QUIC 的 SMB 提供公开预览版：

- Windows Server 2022 Datacenter：Azure Edition（桌面体验）
- Windows Server 2022 Datacenter：Azure Edition (Core)

利用基于 QUIC 的 SMB，用户可在远程办公时通过 QUIC 协议隧道传输 SMB 流量，从而访问文件，而无需使用 VPN。  要了解详细信息，请参阅[基于 QUIC 的 SMB](/windows-server/storage/file-server/smb-over-quic)。  

### <a name="azure-extended-network"></a>Azure 扩展网络

在以下映像中，Azure 扩展网络提供公开预览版：

- Windows Server 2022 Datacenter：Azure Edition（桌面体验）
- Windows Server 2022 Datacenter：Azure Edition (Core)

使用 Azure 扩展网络，可将本地子网扩展到 Azure，使本地虚拟机在迁移到 Azure 时保留其原始的本地专用 IP 地址。 要了解详细信息，请参阅 [Azure 扩展网络](/windows-server/manage/windows-admin-center/azure/azure-extended-network)。  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition 入门

请务必先考虑使用哪些适用于 Windows Server 的 Automanage 功能，然后再选择支持这些功能的相应 VM 映像。  某些 Windows Server Azure Edition 映像仅支持一部分功能，请参阅下表了解详细信息。

### <a name="deciding-which-image-to-use"></a>确定使用哪个映像 

|图像|功能|
|--|--|
| Windows Server 2019 Datacenter：Azure Edition (Core) | 热补丁 | 
|Windows Server 2022 Datacenter：Azure Edition（桌面体验） | 基于 QUIC 的 SMB、扩展网络 | 
| Windows Server 2022 Datacenter：Azure Edition (Core) | 热补丁、基于 QUIC 的 SMB、扩展网络 | 

### <a name="creating-a-vm"></a>创建 VM

若要开始在新的 VM 上使用适用于 Windows Server 的 Automanage 功能，请使用首选方法创建 Azure VM，并选择与要使用的一组[功能](#getting-started-with-windows-server-azure-edition)对应的 Windows Server Azure Edition 映像。  创建 VM 期间可能需要配置这些功能。 你可在各个功能主题（例如[热补丁](automanage-hotpatch.md)）详细了解 VM 配置。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Automanage](automanage-virtual-machines.md)