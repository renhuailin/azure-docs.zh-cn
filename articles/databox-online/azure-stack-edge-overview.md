---
title: Microsoft Azure Stack Edge Pro FPGA 概述 | Microsoft Docs
description: 介绍 Azure Stack Edge Pro FPGA，这是一种使用物理设备进行 Azure 网络传输的存储解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: 47fab96220a3a2bc63864c86bdff210673c01aa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738113"
---
# <a name="what-is-azure-stack-edge-pro-fpga"></a>什么是 Azure Stack Edge Pro FPGA？

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge Pro with FPGA 是具有网络数据传输功能的支持 AI 的边缘计算设备。 本文概述 Azure Stack Edge Pro with FPGA 解决方案、其优势、重要功能和部署方案。

Azure Stack Edge Pro with FPGA 是一种硬件即服务解决方案。 Microsoft 为客户提供了一个具有内置现场可编程门阵列 (FPGA) 的云托管设备，该设备可加速 AI 推理并具有存储网关的所有功能。

Azure Data Box Edge 更名为 Azure Stack Edge。

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Azure Stack Edge Pro FPGA 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- 使用 Azure 机器学习进行推理 - 借助 Azure Stack Edge Pro FPGA，可以运行 ML 模型以获得可在将数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Azure Stack Edge Pro FPGA 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Azure Stack Edge Pro FPGA 上部署 Azure ML 硬件加速模型](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)。

- **预处理数据** - 转换数据，然后将数据发送到 Azure，以创建更具操作的数据集。 使用预处理可以： 

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。 

- 通过网络将数据传输到 Azure - 使用 Azure Stack Edge Pro FPGA 可以既快速又轻松地将数据传输到 Azure，以实现其他计算和分析或用于存档。 

## <a name="key-capabilities"></a>关键功能

Azure Stack Edge Pro FPGA 具有以下功能：

|功能 |说明  |
|---------|---------|
|加速 AI 推断| 通过内置 FPGA 实现。|
|计算       |允许分析、处理、筛选数据。|
|高性能 | 高性能计算和数据传输。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|云托管     |设备和服务通过 Azure 门户进行管理。  |
|离线上传     | 离线模式支持离线上传方案。|
|支持的协议     | 支持用于数据引入的标准 SMB 和 NFS 协议。 <br> 有关受支持版本的详细信息，请参阅 [Azure Stack Edge Pro FPGA 系统要求](azure-stack-edge-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。|
|加密    | BitLocker 支持本地加密数据，并通过 *http* 安全地将数据传输到云中。|
|带宽限制| 中止以限制在高峰时段使用带宽。|
|ExpressRoute | 通过 ExpressRoute 增加了安全性。 使用对等互连配置，从本地设备到云存储终结点的流量将通过 ExpressRoute 传输。 有关详细信息，请参阅 [ExpressRoute 概述](../expressroute/expressroute-introduction.md)。

## <a name="components"></a>组件

Azure Stack Edge Pro FPGA 解决方案包括 Azure Stack Edge 资源、Azure Stack Edge Pro FPGA 物理设备和本地 Web UI。

* Azure Stack Edge Pro FPGA 物理设备：Microsoft 提供的 1U 机架安装式服务器，可配置为将数据发送到 Azure。
    
* Azure Stack Edge 资源：Azure 门户中的一种资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理 Azure Stack Edge Pro FPGA 设备。 使用 Azure Stack Edge 资源可以创建和管理资源、管理共享，以及查看和管理设备与警报。
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   由于 Azure Stack Edge Pro FPGA 的生命周期即将结束，我们不再接受 Azure Stack Edge Pro FPGA 设备的新订单。 如果你是新客户，我们建议先了解如何为工作负载使用 Azure Stack Edge Pro - GPU 设备。 有关详细信息，请参阅[什么是 Azure Stack Edge Pro with GPU](azure-stack-edge-gpu-overview.md)。 有关订购 Azure Stack Edge Pro with GPU 设备的信息，请参阅[为 Azure Stack Edge Pro - GPU 创建新资源](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)。

   如果你是现有客户并需要更换或重置现有的 Azure Stack Edge Pro FPGA 设备，仍可创建新的 Azure Stack Edge 资源。 有关说明，请参阅[为 Azure Stack Edge Pro FPGA 设备创建订单](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)。

* Azure Stack Edge Pro FPGA 本地 Web UI - 使用本地 Web UI 可以运行诊断、关闭和重启 Azure Stack Edge Pro FPGA 设备、查看复制日志，并联系 Microsoft 支持部门以提出服务请求。

    <!--![The Azure Stack Edge Pro FPGA local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    有关使用基于 Web 的 UI 的信息，请转到[使用基于 Web 的 UI 管理 Azure Stack Edge Pro FPGA](azure-stack-edge-manage-access-power-connectivity-mode.md)。

## <a name="region-availability"></a>上市区域

将数据传输到其中的 Azure Stack Edge Pro FPGA 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅 [可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 Azure Stack Edge Pro FPGA 也可以部署在 Azure 政府云中。 有关详细信息，请参阅[什么是 Azure 政府？](../azure-government/documentation-government-welcome.md)。
    
- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Azure Stack Edge Pro FPGA 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。

Azure Stack Edge 服务是一种非区域性服务。 有关详细信息，请参阅 [Azure 中的区域和可用性区域](../availability-zones/az-overview.md)。 Azure Stack Edge 服务不依赖于特定的 Azure 区域，因此可以灵活应对区域范围的服务中断和区域范围的服务中断。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Stack Edge Pro FPGA 系统需求](azure-stack-edge-system-requirements.md)。
- 了解 [Azure Stack Edge Pro FPGA 限制](azure-stack-edge-limits.md)。
- 在 Azure 门户中部署 [Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)。