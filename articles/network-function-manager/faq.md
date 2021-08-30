---
title: 网络功能管理器常见问题解答
titleSuffix: Azure Network Function Manager
description: 了解网络功能管理器的常见问题解答。
author: cherylmc
ms.service: network-function-manager
ms.topic: article
ms.date: 06/30/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: 902b53e48b7d16f06511a0d21495cf6e191f92e2
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253595"
---
# <a name="azure-network-function-manager-faq-preview"></a>Azure 网络功能管理器常见问题解答（预览版）

## <a name="faqs"></a>常见问题解答

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>我是网络功能合作伙伴，想要加入网络功能管理器。 我该如何通过 NFM 提供网络功能？

我们的目标是提供丰富的生态系统，其中有他们选择的 Azure Stack Edge 上提供的网络功能。 若要咨询加入要求，请发送电子邮件至 aznfmpartner@microsoft.com。

### <a name="does-network-function-manager-preview-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>除 Azure Stack Edge Pro with GPU 之外，网络功能管理器预览版是否还支持其他 Azure 边缘设备？

NFM 预览版目前在 Azure Stack Edge Pro with GPU（已正式发布）中提供。 ASE Pro 是一种硬件即服务，旨在运行专门的网络功能，例如移动数据包核心和 SD-WAN 边缘。 该设备配备 6 个物理端口，端口 5 和 6 提供网络加速支持。 请查看 Azure Stack Edge Pro with GPU 设备的[网络接口规范](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications)。 网络功能合作伙伴可利用 SR-IOV 和 DPDK 功能为其网络功能提供更好的网络性能。

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>除了运行网络功能外，Azure Stack Edge Pro with GPU 设备上还提供哪些功能？

Azure Stack Edge (ASE) Pro with GPU 和 Azure 网络功能管理器是 [Azure 专用 MEC](../private-multi-access-edge-compute-mec/index.yml) 解决方案的一部分。 你现可在 ASE 设备上运行专用移动网络和 VM 或基于容器的边缘应用程序。 这样便可构建创新解决方案，为关键商业应用程序提供可预测的 SLA。 Azure Stack Edge Pro 还配有一两个 [GPU](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications)，使你能够在边缘使用视频推理和机器学习等方案。

### <a name="what-is-the-pricing-for-network-function-manager-preview"></a>网络功能管理器预览版的定价是多少？

Azure 网络功能管理器预览版在 Azure Stack Edge Pro 设备上免费提供。 若网络功能合作伙伴使用 NFM 服务提供网络功能，可能需单独付费。 有关定价的详细信息，请咨询你的网络功能合作伙伴。

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>如果我的 Azure Stack Edge Pro 设备处于断开连接或部分连接模式，已部署的网络功能是否会停止工作？

网络功能管理器服务要求 ASE 设备建立网络连接来执行管理操作，以便创建/删除网络功能、监视并故障排除设备上正在运行的网络功能。 如果 ASE 设备上部署了网络功能，并且该设备与基础网络功能断开连接或部分连接，则虚拟机应会继续运行，不会发生任何中断。 部署在这些虚拟机上的网络功能可能有不同的要求（视配置管理而定），以及网络功能合作伙伴的其他网络连接要求。 请联系你的合作伙伴，了解网络连接要求和操作模式。

### <a name="which-regions-are-supported-for-preview-will-you-add-support-for-additional-azure-regions"></a>预览版支持哪些区域？ 是否会添加对其他 Azure 区域的支持？

在预览版期间，网络功能管理器在以下区域提供：

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

Azure Stack Edge Pro with GPU 已在多个国家/地区推出，可用于部署和运行你选择的网络功能。 有关提供 Azure Stack Edge Pro GPU 设备的所有国家/地区的列表，请转到 [Azure Stack Edge Pro GPU 定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)页面。 在“Azure Stack Edge Pro”选项卡上，查看“可用性”部分 ****  。

在预览版期间，可根据法规和数据主权要求，注册 Azure Stack Edge 设备和网络功能管理器资源。 与网络功能管理器资源关联的 Azure 区域用于指导从云服务到物理设备的管理操作。

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>删除用于 Azure Stack Edge 上运行的网络功能的托管应用程序时，网络功能计费是否自动停止？

请咨询你的网络功能合作伙伴，了解使用网络功能管理器部署的网络功能的计费周期。 每家合作伙伴的网络功能计费策略都各不相同。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[概述](overview.md)。
