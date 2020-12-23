---
title: 准备 Azure Stack Edge Pro 设备故障
description: 描述如何获取 Azure Stack Edge Pro 故障设备的替换。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: b437ce7b6894ebefe38b32f27d370d9f8c4bfe80
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369015"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>准备 Azure Stack Edge Pro GPU 设备故障

本文介绍如何在 Azure Stack Edge Pro GPU 设备上保存和备份设备配置和数据，从而帮助你为设备故障做准备。 

本文不包含备份 Azure Stack Edge Pro GPU 设备上部署的 Kubernetes 和 IoT 容器的步骤。 

## <a name="understand-device-failures"></a>了解设备故障

Azure Stack Edge Pro GPU 设备可能出现两种类型的硬件故障。

- 要求更换硬件组件的容许故障。 这些故障将允许你以降级状态运行设备。 这些故障的示例包括单个故障电源单元 (PSU) 或设备上出现单一故障磁盘。 在上述每种情况下，设备都可以继续运行。 请尽快 Microsoft 支持部门联系，以更换发生故障的组件。

- 需要更换整个设备的不可容忍故障，例如，当设备上的两个磁盘出现故障时。 在这些情况下，请立即联系 Microsoft 支持部门。 在确定需要更换设备后，支持部门将会帮助替换 Azure Stack Edge 设备。

若要为无法容忍的故障做好准备，需要在设备上备份以下内容：

- 有关设备配置的信息
- 边缘本地共享和边缘云共享中的数据
- 与设备上运行的 Vm 关联的文件和文件夹


## <a name="back-up-device-configuration"></a>备份设备配置

在设备初始配置过程中，请务必保留 [部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所述的设备配置信息的副本。 在恢复期间，此配置信息将用于新的替换设备。 

## <a name="protect-device-data"></a>保护设备数据

设备数据可以是以下类型之一：

- 边缘云共享中的数据
- 本地共享中的数据
- Vm 上的文件和文件夹

以下部分讨论了在设备上保护每种类型的数据的步骤和建议。

## <a name="protect-data-in-edge-cloud-shares"></a>保护边缘云共享中的数据

你可以创建将设备中的数据分层到 Azure 的边缘云共享。 根据可用的网络带宽，在设备上配置带宽模板，以最大程度地减少发生不可容忍故障时的任何数据丢失。

> [!IMPORTANT]
> 如果设备具有无法容忍的故障，则不是从设备到 Azure 的分层的本地数据可能会丢失。 

## <a name="protect-data-in-edge-local-shares"></a>保护边缘本地共享中的数据

如果要部署 Kubernetes 或 IoT Edge，请将配置为在本地设备上保存应用程序数据，不与 Azure 存储同步。 数据存储在设备上的共享上。 你可能会发现，备份这些共享中的数据非常重要。

以下第三方数据保护解决方案可为本地 SMB 或 NFS 共享中的数据提供备份解决方案。 

| 第三方软件           | 对解决方案的引用                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请与 Veritas 联系。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |


## <a name="protect-files-and-folders-on-vms"></a>保护 Vm 上的文件和文件夹

Azure Stack Edge 适用于 Azure 备份和其他第三方数据保护解决方案，以提供备份解决方案来保护设备上部署的 Vm 中包含的数据。 下表列出了可从中进行选择的可用解决方案的引用。


| 备份解决方案        | 支持的 OS   | 参考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| 用于 Azure 备份的 (MARS) 代理 Microsoft Azure 恢复服务 | Windows        | [关于 MARS 代理](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 集成，备份 & 恢复解决方案 brief](https://www.cohesity.com/solution/cloud/azure) <br>有关详细信息，请联系 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>有关详细信息，请联系 Commvault。                          |
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> 有关详细信息，请与 Veritas 联系。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |


## <a name="next-steps"></a>后续步骤

- 了解如何 [从失败的 Azure Stack Edge PRO GPU 设备进行恢复](azure-stack-edge-gpu-recover-device-failure.md)。