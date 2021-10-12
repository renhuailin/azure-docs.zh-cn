---
title: Azure Stack Edge Mini R 概述 | Microsoft Docs
description: 介绍 Azure Stack Edge Mini R，这是一种使用带有电池的便携式物理设备通过 Wi-Fi 传输到 Azure 的存储解决方案，适用于军事应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/04/2021
ms.author: alkohli
ms.openlocfilehash: df6838d1616d27c12d7176df0041a35d0d773f83
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545108"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>什么是 Azure Stack Edge Mini R？

Azure Stack Edge Mini R 是一种专为在恶劣环境中使用而设计的超便携式加固型边缘计算设备。 Azure Stack Edge Mini R 以“硬件即服务”解决方案的形式提供。 Microsoft 为你提供了一个云托管设备，该设备充当网络存储网关并具有支持加速 AI 推理的内置式视觉处理单元 (VPU)。

本文概述了 Azure Stack Edge Mini R 解决方案、重要功能以及可以部署此设备的场景。


## <a name="key-capabilities"></a>关键功能

Azure Stack Edge Mini R 具有以下功能：

|功能 |说明  |
|---------|---------|
|加固型硬件| 专为恶劣环境设计的加固型硬件。|
|超便携| 超便携、电池供电的外形规格。|
|云托管|设备和服务通过 Azure 门户进行管理。|
|Edge 计算工作负载|允许分析、处理、筛选数据。<br>支持 VM 和容器化工作负载。 <ul><li>有关 VM 工作负荷的信息，请参阅 [Azure Stack Edge 上的 VM 概述](azure-stack-edge-gpu-virtual-machine-overview.md)。</li> <li>有关容器化工作负荷，请参阅 [Azure Stack Edge 上的 Kubernetes 概述](azure-stack-edge-gpu-kubernetes-overview.md)。</li></ul>  |
|加速 AI 推断| 由 Intel Movidius Myriad X VPU 提供支持。 |
|有线和无线 | 允许进行有线数据传输和无线数据传输。|
|数据访问     | 使用云 API 从 Azure 存储 Blob 和 Azure 文件中直接访问数据，以便在云中进行其他数据处理。 设备带有本地缓存，以便快速访问最近使用的文件。|
|断开连接模式|  可以选择通过 Azure Stack Hub 管理设备和服务。 在脱机模式下部署、运行和管理应用程序。 <br> 离线模式支持离线上传方案。|
|支持的文件传输协议      |支持用于数据引入的标准 SMB、NFS 和 REST 协议。 <br> 有关支持的版本的详细信息，请转到 [Azure Stack Edge Mini R 系统要求](azure-stack-edge-gpu-system-requirements.md)。|
|数据刷新     | 可以使用云中的最新内容刷新本地文件。 <br> 有关详细信息，请参阅[刷新 Azure Stack Edge 上的共享](azure-stack-edge-gpu-manage-shares.md#refresh-shares)。|
|双重加密    | 使用自加密驱动器提供第一层加密。 VPN 提供第二层加密。 BitLocker 支持本地加密数据，并通过 https 安全地将数据传输到云中。 <br> 有关详细信息，请参阅[在 Azure Stack Edge Pro R 设备上配置 VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)。|
|带宽限制| 中止以限制在高峰时段使用带宽。 <br> 有关详细信息，请参阅[管理 Azure Stack Edge 上的带宽计划](azure-stack-edge-gpu-manage-bandwidth-schedules.md)。|
|轻松排序| 通过 Azure Edge Hardware Center（预览版）对设备进行批量排序和跟踪。 <br> 有关详细信息，请参阅[通过 Azure Edge Hardware Center 订购设备](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)。|

## <a name="use-cases"></a>用例

下面介绍各种方案，其中 Azure Stack Edge Mini R 可用于加快边缘的机器学习 (ML) 推理并对数据进行预处理，然后再将该数据发送到 Azure。

- 使用 Azure 机器学习进行推理 - 借助 Azure Stack Edge Mini R，可以运行 ML 模型以获得可在将数据发送到云之前执行的快速结果。 可以选择传输完整的数据集以继续重新训练并改进 ML 模型。 有关如何在 Azure Stack Edge Mini R 设备上使用 Azure ML 硬件加速模型的详细信息，请参阅[在 Azure Stack Edge Mini R 上部署 Azure ML 硬件加速模型](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)。

- 预处理数据 - 在将数据发送到 Azure 之前通过计算选项（如容器或虚拟机）转换数据，以创建更具操作性的数据集。 使用预处理可以：

    - 聚合数据。
    - 修改数据，例如删除个人数据。
    - 用于优化存储和带宽，或用于进一步分析的子集数据。
    - 分析和应对 IoT 事件。

- 通过网络将数据传输到 Azure - 使用 Azure Stack Edge Mini R 可以既快速又轻松地将数据传输到 Azure，以实现其他计算和分析或用于存档。

## <a name="components"></a>组件

Azure Stack Edge Mini R 解决方案包括 Azure Stack Edge 资源、Azure Stack Edge Mini R 超便携式加固型物理设备和本地 Web UI。

* **Azure Stack Edge Mini R 物理设备** - Microsoft 提供的超便携式加固型计算和存储设备。 该设备具有板载电池，重量不到 7 磅。

    ![Azure Stack Edge Mini R 设备](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    有关详细信息，请转到[为 Azure Stack Edge Mini R 设备创建订单](azure-stack-edge-mini-r-deploy-prep.md#create-a-new-resource)。

* Azure Stack Edge 资源 - Azure 门户中的一种资源，使用该资源可以通过 Web 界面（可从不同的地理位置访问该界面）管理加固型 Azure Stack Edge Mini R 设备。 使用 Azure Stack Edge 资源可以创建和管理资源、查看和管理设备与警报，以及管理共享。  

* **Azure Stack Edge Mini R 本地 Web UI** - Azure Stack Edge Mini R 设备上基于浏览器的本地用户界面，主要用于设备的初始配置。 使用本地 Web UI 还可以运行诊断、关闭和重启 Azure Stack Edge Pro 设备、查看复制日志，并联系 Microsoft 支持部门以提出服务请求。

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]

## <a name="region-availability"></a>上市区域

将数据传输到其中的 Azure Stack Edge Mini R 物理设备、Azure 资源和目标存储帐户不一定非要位于同一区域。

- **资源可用性** - 有关 Azure Stack Edge 资源可用的所有区域的列表，请转到 [各区域提供的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 

- **设备可用性** - 有关 Azure Stack Edge Mini R 设备可供使用的所有国家/地区的列表，请转到 [Azure Stack Edge Mini R 定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR)的“Azure Stack Edge Mini R”选项卡中的“可用性”部分。

- **目标存储帐户** - 存储数据的存储帐户可在所有 Azure 区域中获得。 存储帐户存储 Azure Stack Edge Mini R 数据的区域应靠近设备所在位置，以便获得最佳性能。 远离设备的存储帐户会导致长时间的延迟和性能下降。

Azure Stack Edge 服务是一种非区域性服务。 有关详细信息，请参阅 [Azure 中的区域和可用性区域](../availability-zones/az-overview.md)。 Azure Stack Edge 服务不依赖于特定的 Azure 区域，因此可以灵活应对区域范围的服务中断和区域范围的服务中断。

有关为 Azure Stack Edge 服务、设备和数据存储选择区域的注意事项的讨论，请参阅[为 Azure Stack Edge 选择区域](azure-stack-edge-gpu-regions.md)。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Stack Edge Mini R 系统要求](azure-stack-edge-gpu-system-requirements.md)。