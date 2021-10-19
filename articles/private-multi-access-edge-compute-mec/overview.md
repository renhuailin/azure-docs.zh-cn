---
title: Azure 专用多接入边缘计算
description: 了解 Azure 专用多接入边缘计算 (MEC) 解决方案，它可将从云管理的 Microsoft 计算、网络和应用程序服务产品组合融合到一起。
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: 1875a2cfaa89a6eeeb45ae2efe84959ff6771268
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707329"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>什么是 Azure 专用多接入边缘计算？

Azure 专用多接入边缘计算 (MEC) 是可将从云管理的 Microsoft 计算、网络和应用程序服务产品组合融合到一起的解决方案。 该解决方案使运营商和系统集成商能够在企业边缘提供高性能、低延迟连接和 IoT 应用程序，迎接下一波企业数字化转型。 

Azure 专用 MEC 是专用边缘区域的演进，将可行方案的范围从单一平台和服务扩展到了利用多个平台和功能的解决方案。 这些功能包括边缘服务和应用程序、边缘网络功能、边缘计算选项以及边缘无线电和设备。 这些功能在更靠近终端设备的位置处理数据，有助于改善延迟和吞吐量敏感型用户方案，例如视频分析、实时机器人和全球规模的混合 IoT 用例。 客户和合作伙伴可以受益于一整套 Azure 服务和生态系统技术组件，从而快速方便地构建、部署和管理解决方案。 

## <a name="benefits-to-customers-and-partners"></a>为客户和合作伙伴带来的好处
- 企业客户：
    - 访问不断扩充的 Azure 服务产品、ISV 和开发人员生态系统组合。
    - 选择可信的行业定制解决方案。
    - 完全集成的技术堆栈，初始规模只需占用 1U 机架空间。

- 电信和系统集成商合作伙伴：
    - 轻松为其客户采购、部署、管理 5G 与边缘设施并通过这些设施为其客户盈利。
    - 选择产品、云服务和应用程序的特选生态系统。
    - 访问 Azure 开发人员生态系统。

- 应用程序 ISV：
    - 用于开发超低延迟应用程序的全功能平台。 
    - 一致的开发人员体验和成熟的工具与资源。
    - 通过 Microsoft 市场进行大规模分发

## <a name="microsoft-capabilities"></a>Microsoft 功能
Azure 专用 MEC 包括来自 Microsoft 的多项功能。 这些功能包括网络功能产品、管理服务以及硬件基础结构和服务的组合。 

### <a name="azure-network-functions-offered-via-marketplace"></a>通过市场提供的 Azure 网络功能

Metaswitch Fusion Core：Fusion Core 是一个完全容器化的 5G Core 解决方案，支持在通过 4G 或 5G 无线电连接到数据网络的 IoT 设备之间建立连接所需的全部网络功能。 该解决方案提供以下主要优势：
 - 可通过 Azure 市场门户在 Azure Stack Edge 上轻松部署和预配。
 - 25 Gbps 高性能吞吐量，超低的计算资源占用量（四个物理核心）。
 - 支持 4G 和 5G 独立接入类型。
 - 内置工具可用于提供以企业为中心的服务保障和 KPI 仪表板。 
 
Fusion Core 使 ISV 能够在 IoT Edge 应用程序所在的同一 Azure Stack Edge 节点上部署实时视频分析等应用程序。 

经证实的专用网络服务：经证实的专用网络服务是一个 Azure 市场，为要向企业提供 4G 和 5G 托管服务产品的移动网络运营商和托管服务提供商提供托管的专用网络服务。 借助 APNS，运营商可为企业提供移动运营商级专用移动网络，使他们能够运行和运营需要低延迟、高带宽和端到端安全性的业务关键型应用程序。 它与移动网络运营商集成，在专用和公共运营商网络之间提供完全移动性。 凭借其自动化和简化的操作，APNS 可跨数千个企业边缘位置提供可伸缩性，并使用 Azure 在专用网络和企业应用程序上提供增强的安全性。 它可以让客户灵活地部署整个移动核心 - 部署到网络边缘、全部部署到云中，或者采用混合模式（在云中提供控制平面，在企业边缘提供用户平面）。 

### <a name="azure-management-services"></a>Azure 管理服务

Azure 网络功能管理器 (NFM)：借助 Azure NFM，可以使用一致的 Azure 工具和界面将网络功能部署到边缘。 使用此服务可将数据包核心和 SD-WAN 网络功能部署到 Azure Stack Edge。 有关详细信息，请参阅 [Azure 网络功能管理器](../network-function-manager/overview.md)。

**已启用 Arc 的 Kubernetes**：通过已启用 Azure Arc 的 Kubernetes，可以在 Azure 内部或外部附加和配置 Kubernetes 群集。 可以使用基于策略的部署进行大规模监视和管理，并大规模应用一致的安全配置。 已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 有关详细信息，请参阅 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)。

### <a name="azure-stack-hardware-and-services"></a>Azure Stack 硬件和服务
Azure Stack Edge：Azure Stack Edge 提供可将计算、存储和智能融合到创建数据的边缘的设备组合。 设备可装入 1U 机架空间，搭载 1-2 个 NVIDIA T4 GPU。 使用 Azure IoT Edge，可以从 IoT 中心部署和管理容器，并与 Azure Stack Edge 上的 Azure IoT 解决方案集成。 Azure Stack Edge Pro SKU 已经过认证，可在边缘上运行网络功能。 有关详细信息，请参阅 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)。

Azure Stack HCI：Azure Stack HCI 是作为 Azure 服务提供的新型超融合基础设施 (HCI) 操作系统，提供最新的安全、性能和功能更新。 使用现有工具和流程在数据中心或边缘部署和运行 Windows 与 Linux 虚拟机 (VM)。 使用 Azure 备份、Azure Monitor 和 Azure 安全中心将数据中心扩展到云。 有关详细信息，请参阅 [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/)。

### <a name="application-services"></a>应用程序服务

Azure IoT Edge 运行时：借助 Azure IoT Edge 运行时，可以使用与云原生工作负载相同的工具和安全态势跨边缘计算设备管理和部署云工作负载。 有关详细信息，请参阅 [Azure IoT Edge 运行时](/windows/ai/windows-ml-container/iot-edge-runtime)。

Azure IoT 中心 Azure IoT Edge 运行时：借助 Azure IoT Edge 运行时，可以使用与云原生工作负载相同的工具和安全态势跨边缘计算设备管理和部署云工作负载。 有关详细信息，请参阅 [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/)。

Azure IoT Central：Azure IoT Central 是一个托管应用程序平台，它以服务的形式实现设备管理和数据引入，提供可预测的定价模式并内置了全局缩放功能。 有关详细信息，请参阅 [Azure IoT Central](https://azure.microsoft.com/services/iot-central/)。

Azure 数字孪生：使用 Azure 数字孪生可以在考虑到空间关系、使用模式和其他可将设备群转化为物理资产或环境的数字副本的业务上下文的前提下，在设备传感器的业务上下文中为其建模。 有关详细信息，请参阅 [Azure 数字孪生](https://azure.microsoft.com/services/digital-twins/)。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Metaswitch Fusion Core](metaswitch-fusion-core-overview.md)
- 详细了解[经证实的专用网络服务](affirmed-private-network-service-overview.md)
