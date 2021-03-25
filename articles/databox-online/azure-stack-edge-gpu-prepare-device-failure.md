---
title: 为 Azure Stack Edge Pro 设备故障做好准备
description: 描述如何为 Azure Stack Edge Pro 故障设备进行所需更换。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442972"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>为 Azure Stack Edge Pro GPU 设备故障做好准备

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

本文详述了如何保存和备份 Azure Stack Edge Pro GPU 设备上的设备配置和数据，从而为设备故障做好准备。 

本文不包含 Azure Stack Edge Pro GPU 设备上部署的 Kubernetes 和 IoT 容器的相关备份步骤。 

## <a name="understand-device-failures"></a>了解设备故障

Azure Stack Edge Pro GPU 设备可能出现两种类型的硬件故障。

- 容许的故障，发生这种故障后需要更换硬件组件。 发生这种故障后设备仍能在降级状态下运行。 这类故障的例子包括：设备上出现单个故障电源单元 (PSU) 或单个故障磁盘。 在上述每种情况下，设备仍可以继续运行。 应尽快联系 Microsoft 支持部门，以更换发生故障的组件。

- 不容许的故障，发生这种故障后需要更换整个设备，例如当设备上的两个磁盘出现故障时。 在这种情况下，请立即联系 Microsoft 支持部门。 在确定需要更换设备后，支持部门会帮助更换 Azure Stack Edge 设备。

若要为不容许的故障做好准备，需要在设备上备份以下内容：

- 有关设备配置的信息
- 边缘本地共享和边缘云共享中的数据
- 与设备上运行的 VM 关联的文件和文件夹


## <a name="back-up-device-configuration"></a>备份设备配置

在设备初始配置过程中，请务必保留[部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所述的设备配置信息的副本。 在设备恢复期间，需要在新的替换设备上应用此配置信息。 

## <a name="protect-device-data"></a>保护设备数据

设备数据可以是以下类型之一：

- Edge 云共享中的数据
- 本地共享中的数据
- VM 上的文件和文件夹

以下部分介绍保护设备上每种类型的数据的相关步骤和建议。

## <a name="protect-data-in-edge-cloud-shares"></a>保护边缘云共享中的数据

可以创建边缘云共享，用于将数据从设备分层到 Azure。 根据可用的网络带宽，在设备上配置带宽模板，以最大程度减小发生不容许的故障时的数据丢失。

> [!IMPORTANT]
> 如果设备发生了不容许的故障，未从设备分层到 Azure 的本地数据可能会丢失。 

## <a name="protect-data-in-edge-local-shares"></a>保护边缘本地共享中的数据

如果要部署 Kubernetes 或 IoT Edge，请配置为在设备上本地保存应用程序数据且不与 Azure 存储同步。 数据存储在设备上的共享上。 你可能会发现，备份这些共享中的数据非常重要。

以下第三方数据保护解决方案可提供针对本地 SMB 或 NFS 共享中的数据的备份解决方案。 

| 第三方软件           | 解决方案参考信息                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请联系 Veritas。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |


## <a name="protect-files-and-folders-on-vms"></a>保护 VM 上的文件和文件夹

Azure Stack Edge 与 Azure 备份和其他第三方数据保护解决方案协作提供了用于保护设备上部署的 VM 中包含的数据的备份解决方案。 下表列出了可供选择的可用解决方案的参考信息。


| 备份解决方案        | 支持的 OS   | 参考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| 适用于 Azure 备份的 Microsoft Azure 恢复服务 (MARS) 代理 | Windows        | [关于 MARS 代理](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 集成、备份和恢复解决方案简介](https://www.cohesity.com/solution/cloud/azure) <br>有关详细信息，请联系 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>有关详细信息，请联系 Commvault。                          |
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> 有关详细信息，请联系 Veritas。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |


## <a name="next-steps"></a>后续步骤

- 了解如何[从出现故障的 Azure Stack Edge Pro GPU 设备恢复](azure-stack-edge-gpu-recover-device-failure.md)。