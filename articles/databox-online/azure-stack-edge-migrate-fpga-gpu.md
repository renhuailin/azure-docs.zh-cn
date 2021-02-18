---
title: Azure Stack Edge Pro FPGA 迁移到 GPU 物理设备的指南
description: 本指南包含将工作负荷从 Azure Stack Edge Pro FPGA 设备迁移到 Azure Stack Edge Pro GPU 设备的说明。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: ce063e27620c3b0ccec9336e7a45ebb2ee1ad10d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100877"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>将工作负荷从 Azure Stack Edge Pro FPGA 迁移到 Azure Stack Edge Pro GPU

本文介绍如何将工作负荷和数据从 Azure Stack Edge Pro FPGA 设备迁移到 Azure Stack Edge Pro GPU 设备。 迁移过程涉及迁移的概述，其中包括两台设备之间的比较、迁移注意事项、详细步骤，以及验证后的清理。

<!--Azure Stack Edge Pro FPGA devices will reach end-of-life in February 2024. If you are considering new deployments, we recommend that you explore Azure Stack Edge Pro GPU devices for your workloads.-->

## <a name="about-migration"></a>关于迁移

迁移是将工作负荷和应用程序数据从一个存储位置转移到另一个位置的过程。 这需要将组织的当前数据从一台存储设备完全复制到另一台存储设备（最好能够避免中断或禁用活动的应用程序），然后将所有输入/输出 (I/O) 活动重定向到新设备。 

此迁移指南提供了将数据从 Azure Stack Edge Pro FPGA 设备迁移到 Azure Stack Edge Pro GPU 设备所需步骤的分步演练。 本文档面向那些负责在数据中心运营、部署和管理 Azure Stack Edge 设备的信息技术 (IT) 专业人员和知识工作者。 

在本文中，将 Azure Stack Edge Pro FPGA 设备称为源设备，将 Azure Stack Edge Pro GPU 设备称为目标设备。 

## <a name="comparison-summary"></a>比较摘要

本节提供 Azure Stack Edge Pro GPU 与 Azure Stack Edge Pro FPGA 设备之间功能的对比摘要。 源设备和目标设备中的硬件大致相同，只是硬件加速卡和存储容量不同。 

|    功能  | Azure Stack Edge Pro GPU（目标设备）  | Azure Stack Edge Pro FPGA（源设备）|
|----------------|-----------------------|------------------------|
| 硬件       | 硬件加速：1 个或 2 个 Nvidia T4 GPU <br> 计算、内存、网络接口、电源单元、电源线规格与 FPGA 设备相同。  | 硬件加速：Intel Arria 10 FPGA <br> 计算、内存、网络接口、电源单元、电源线规格与 GPU 设备相同。          |
| 可用存储 | 4.19 TB <br> 预留空间供奇偶校验复原和内部使用之后 | 12.5 TB <br> 预留空间供内部使用之后 |
| 安全性       | 证书 |                                                     |
| 工作负荷      | IoT Edge 工作负荷 <br> VM 工作负荷 <br> Kubernetes 工作负载| IoT Edge 工作负荷 |
| 定价        | [定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>迁移计划

若要创建迁移计划，请考虑以下信息：

- 制定迁移计划。 
- 在迁移数据时，可能会经历停机时间。 建议你在停机维护时段内计划迁移，因为此过程会造成中断。 如本文档后面所述，你将在此停机时间内设置和还原配置。
- 了解停机时间总时长，并将其传达给所有利益干系人。
- 确定需要从源设备迁移的本地数据。 作为预防措施，确保现有存储中的所有数据都有最近的备份。 


## <a name="migration-considerations"></a>迁移注意事项 

在进行迁移之前，请考虑以下信息： 

- Azure Stack Edge Pro GPU 设备不能针对 Azure Stack Edge Pro FPGA 资源进行激活。 应按照[创建 Azure Stack Edge Pro GPU 订单](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)中的说明，为 Azure Stack Edge Pro GPU 设备创建新资源。
- 需要将在使用 FPGA 的源设备上部署的机器学习模型更改为 GPU 目标设备。 有关模型的帮助，可以联系 Microsoft 支持部门。 在源设备上部署的自定义模型如果未使用 FPGA（仅使用 CPU），在目标设备上应该会按原样工作（使用 CPU）。
- 可能需要更改在源设备上部署的 IoT Edge 模块，才能在目标设备上成功部署这些模块。 
- 源设备支持 NFS 3.0 和 4.1 协议。 目标设备仅支持 NFS 3.0 协议。
- 源设备支持 SMB 和 NFS 协议。 目标设备除了支持将 SMB 和 NFS 协议用于共享之外，还支持通过 REST 协议进行存储。
- 源设备上的共享访问通过 IP 地址实现，而目标设备上的共享访问是通过设备名称实现的。

## <a name="migration-steps-at-a-glance"></a>升级步骤速览

下表总结了迁移的总体流程，介绍了迁移所需的步骤，以及执行这些步骤的位置。

| 阶段 | 步骤| 设备 |
|---------------|-------------|----------------|
| 准备源设备*       | 1.记录配置数据 <br>2.备份共享数据 <br>3.准备 IoT Edge 工作负荷| 源设备  |
| 准备目标设备*       |1.创建新订单 <br>2.配置并激活| 目标设备  |
| 迁移数据       | 1.从共享迁移数据 <br>2.重新部署 IoT Edge 工作负荷| 目标设备  |
| 验证数据            |验证已迁移的数据 |目标设备  |
| 清理、返回              |擦除数据并返回| 源设备  |

*可以并行准备源设备和目标设备。

## <a name="prepare-source-device"></a>准备源设备

准备工作包括确定 Edge 云共享、Edge 本地共享，以及部署在设备上的 IoT Edge 模块。 

### <a name="1-record-configuration-data"></a>1.记录配置数据

通过本地 UI 在源设备上执行下面的步骤。

记录源设备上的配置数据。 使用[部署清单](azure-stack-edge-gpu-deploy-checklist.md)帮助你记录设备配置。 在迁移过程中，你将使用此配置信息来配置新的目标设备。 

### <a name="2-back-up-share-data"></a>2.备份共享数据

设备数据可以是以下类型之一：

- Edge 云共享中的数据
- 本地共享中的数据

#### <a name="data-in-edge-cloud-shares"></a>Edge 云共享中的数据

Edge 云共享将层级数据从你的设备共享到 Azure。 通过 Azure 门户在源设备上执行下面的步骤。 

- 列出你在源设备上拥有的所有 Edge 云共享和用户。
- 列出你拥有的所有带宽计划。 你将在目标设备上重新创建这些带宽计划。
- 根据可用的网络带宽，在设备上配置带宽计划，最大限度地将数据分层到云。 这将最大限度地减少设备上的本地数据。
- 确保共享完全分层到云。 通过在 Azure 门户中检查共享状态可以确认这一点。  

#### <a name="data-in-edge-local-shares"></a>Edge 本地共享中的数据

Edge 本地共享中的数据保留在设备上。 通过 Azure 门户在源设备上执行下面的步骤。 

- 列出你的设备上的 Edge 本地共享。
- 由于这是数据的一次性迁移，请将 Edge 本地共享数据的副本创建到另一台本地服务器上。 可以使用复制工具（如 `robocopy` (SMB) 或 `rsync` (NFS)）复制数据。 你也可能已经部署了第三方数据保护解决方案来备份本地共享中的数据。 以下第三方解决方案支持用于 Azure Stack Edge Pro FPGA 设备：

    | 第三方软件           | 解决方案参考信息                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请联系 Veritas。   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |


### <a name="3-prepare-iot-edge-workloads"></a>3.准备 IoT Edge 工作负荷

- 如果你已经部署了 IoT Edge 模块并使用 FPGA 加速，则可能需要修改这些模块，才能让这些模块在 GPU 设备上运行。 按照[修改 IoT Edge 模块](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)中的说明进行操作。 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>准备目标设备

### <a name="1-create-new-order"></a>1.创建新订单

需要为目标设备创建新订单（和新资源）。 必须针对 GPU 资源而不是针对 FPGA 资源激活目标设备。

若要下达订单，请在 Azure 门户中[创建新的 Azure Stack Edge 资源](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)。


### <a name="2-set-up-activate"></a>2.设置、激活

需要针对先前创建的新资源设置和激活目标设备。 

请按照下面的步骤通过 Azure 门户配置目标设备：

1. 收集[部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所需的信息。 可以使用从源设备配置中保存的信息。 
1. [解包](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)、[装载机架](azure-stack-edge-gpu-deploy-install.md#rack-the-device)，并[将电缆接到设备](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。 
1. [连接到设备的本地 UI](azure-stack-edge-gpu-deploy-connect.md)。
1. 使用一组不同于旧设备使用的 IP 地址（如果使用静态 IP）来配置网络。 请参阅如何[配置网络设置](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
1. 指定与旧设备相同的设备名称，并提供一个 DNS 域。 请参阅如何[配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。
1. 在新设备上配置证书。 请参阅如何[配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。
1. 从 Azure 门户获取激活密钥并激活新设备。 请参阅如何[激活设备](azure-stack-edge-gpu-deploy-activate.md)。

你现在已准备好还原共享数据，并部署在旧设备上运行的工作负荷。

## <a name="migrate-data"></a>迁移数据

现在将源设备中的数据复制到目标设备上的 Edge 云共享和 Edge 本地共享。

### <a name="1-from-edge-cloud-shares"></a>1.从 Edge 云共享

请按照以下步骤操作，在目标设备上同步 Edge 云共享上的数据：

1. [添加共享](azure-stack-edge-j-series-manage-shares.md#add-a-share)，使其与源设备上创建的共享名称相对应。 请确保在创建共享时，将“选择 blob 容器”设置为“使用现有容器”选项，然后选择之前设备使用的容器。
1. [添加用户](azure-stack-edge-j-series-manage-users.md#add-a-user)，使其有权访问之前的设备。
1. [刷新来自 Azure 的共享数据](azure-stack-edge-j-series-manage-shares.md#refresh-shares)。 这会将现有容器中的所有云数据提取到共享中。
1. 重新创建要与共享关联的带宽计划。 有关详细步骤，请参阅[添加带宽计划](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule)。


### <a name="2-from-edge-local-shares"></a>2.从 Edge 本地共享

你可能已经部署第三方备份解决方案来保护 IoT 工作负荷的本地共享数据。 现在需要还原该数据。

在完全配置替换设备后，启用设备的本地存储。 

按照以下步骤操作，从本地共享恢复数据：

1. [在设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. 在目标设备上添加所有本地共享。 请参阅[添加本地共享](azure-stack-edge-j-series-manage-shares.md#add-a-local-share)中的详细步骤。
1. 在源设备上访问 SMB 共享将使用 IP 地址，而在目标设备上，将使用设备名称。 请参阅[连接到 Azure Stack Edge Pro GPU 上的 SMB 共享](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share)。 若要在目标设备上连接到 NFS 共享，需要使用与该设备关联的新 IP 地址。 请参阅[连接到 Azure Stack Edge Pro GPU 上的 NFS 共享](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share)。 

    如果通过 SMB/NFS 将共享数据复制到中间服务器，则可以将此数据复制到目标设备上的共享中。 如果源设备和目标设备都联机，还可以直接从源设备复制数据。

    如果你曾经使用第三方软件备份本地共享中的数据，则需要运行所选的数据保护解决方案所提供的恢复过程。 请参阅下表中的参考信息。

    | 第三方软件           | 解决方案参考信息                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 有关详细信息，请联系 Commvault。 |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 有关详细信息，请联系 Veritas。   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 有关详细信息，请联系 Veeam。 |

### <a name="3-redeploy-iot-edge-workloads"></a>3.重新部署 IoT Edge 工作负荷

准备好 IoT Edge 模块后，你将需要在目标设备上部署 IoT Edge 工作负荷。 如果在部署 IoT Edge 模块时遇到任何错误，请参阅：

- [Azure IoT Edge 的常见问题和解决方法](../iot-edge/troubleshoot-common-errors.md)，和 
- [IoT Edge 运行时错误][通过 Windows PowerShell 管理 Azure Stack Edge Pro GPU 设备](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors)。

## <a name="verify-data"></a>验证数据

迁移后，请验证所有数据是否均已迁移，以及是否已在目标设备上部署了工作负荷。

## <a name="erase-data-return"></a>擦除数据、返回

数据迁移完成后，请擦除本地数据并返回源设备。 按照[返回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)中的步骤操作。


## <a name="next-steps"></a>后续步骤

[了解如何在 Azure Stack Edge Pro GPU 设备上部署 IoT Edge 工作负荷](azure-stack-edge-gpu-deploy-compute-module-simple.md)
