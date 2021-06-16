---
title: 监视 Azure NetApp 文件卷的容量 | Microsoft Docs
description: 介绍监视 Azure NetApp 文件卷的容量利用率的方法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 14bbd15fec0015073eefbe2421d03a73fac45d75
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968387"
---
# <a name="monitor-the-capacity-of-a-volume"></a>监视卷的容量  

本文介绍监视 Azure NetApp 文件卷的容量利用率的方法。  

## <a name="using-windows-or-linux-clients"></a>使用 Windows 或 Linux 客户端

本部分说明如何使用 Windows 或 Linux 客户端监视卷容量。 本部分中所述的方案假设在 4 TiB 的超级服务级别容量池中配置了一个 1 TiB 大小（配额）的卷。 

### <a name="windows-smb-clients"></a>Windows (SMB) 客户端

可以使用 Windows 客户端通过网络映射驱动器属性来检查卷的已用容量和可用容量。 可以使用以下两种方法之一： 

* 转到“文件资源管理器”，右键单击映射驱动器，然后选择“属性”以显示容量。  

    [ ![显示资源管理器驱动器属性和卷属性的屏幕截图。](../media/azure-netapp-files/monitor-explorer-drive-properties.png) ](../media/azure-netapp-files/monitor-explorer-drive-properties.png#lightbox)

* 在命令提示符处使用 `dir` 命令： 

    ![显示使用 dir 命令显示容量的屏幕截图。](../media/azure-netapp-files/monitor-volume-properties-dir-command.png) 

使用文件资源管理器或 `dir` 命令时，可用空间是准确的。 但是，在卷上生成快照时，已消耗/已用空间将是一个估计值。 [已消耗的快照容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) 将计入卷上的总消耗空间。 若要获取绝对卷消耗（包括快照使用的容量），请使用 Azure 门户中的 [Azure NetApp 指标](azure-netapp-files-metrics.md#volumes)。 

### <a name="linux-nfs-clients"></a>Linux (NFS) 客户端 

Linux 客户端可以使用 [df 命令](https://linux.die.net/man/1/df)检查卷的已用容量和可用容量。  

`-h` 选项以用户可读的格式显示大小，包括已用空间和可用空间，大小单位为 M、G 和 T。

以下快照显示了 Linux 中的卷容量报告：

![显示 Linux 中的卷容量报告的屏幕截图。](../media/azure-netapp-files/monitor-volume-properties-linux-command.png) 

使用 `df` 命令时，可用空间是准确的。 但是，在卷上生成快照时，已消耗/已用空间将是一个估计值。 [已消耗的快照容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots) 将计入卷上的总消耗空间。 若要获取绝对卷消耗（包括快照使用的容量），请使用 Azure 门户中的 [Azure NetApp 指标](azure-netapp-files-metrics.md#volumes)。 

## <a name="using-azure-portal"></a>使用 Azure 门户
Azure NetApp 文件利用标准 [Azure Monitor](../azure-monitor/overview.md) 功能。 因此，可以使用 Azure Monitor 来监视 Azure NetApp 文件卷。  

## <a name="using-azure-cli"></a>使用 Azure CLI  

可以使用 [Azure 命令行 (CLI) 工具](azure-netapp-files-sdk-cli.md)的 [`az netappfiles volume`](/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true) 命令来监视卷。
 
## <a name="using-rest-api"></a>使用 REST API  

请参阅 [Azure NetApp 文件的 REST API](azure-netapp-files-develop-with-rest-api.md) 和[使用适用于 Azure NetApp 文件的 PowerShell REST API](develop-rest-api-powershell.md)。 

Azure NetApp 文件的 REST API 规范和示例代码可通过[资源管理器 GitHub 目录](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)获得。 

## <a name="next-steps"></a>后续步骤

* [了解卷配额](volume-quota-introduction.md)
* [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
* [重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [容量管理常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)