---
title: 什么是 Azure 上的 Affirmed 专用网络服务？
description: 了解 Azure 上适用于专用 LTE/5G 网络的 Affirmed 专用网络服务解决方案。
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: ca2327ee8e9245698dc8d9b57b2c5bf2bf8e6881
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458517"
---
# <a name="what-is-affirmed-private-network-service-on-azure"></a>什么是 Azure 上的 Affirmed 专用网络服务？

Affirmed 专用网络服务 (APNS) 是一种托管网络服务产品，专为托管服务提供商和移动网络运营商而创建，用于向企业提供专用 LTE 和专用 5G 解决方案。

Affirmed 将其移动核心技术与 Azure 功能相结合，为专用 LTE/5G 网络创建完整的统包解决方案，以帮助运营商和企业利用托管网络和移动边缘。 云管理和自动化组合允许托管服务提供商提供完全托管的基础结构，还为运营商提供了完整的端到端解决方案，以从 Azure 市场中提供的丰富合作伙伴生态系统中选取最佳无线访问网络、SIM 和 Azure 服务。 该解决方案由五个组件组成：

- 云原生移动核心：此组件符合 3GPP 标准，支持适用于 4G 和 5G 的网络功能，并且具有本地位于移动核心内的虚拟网络探测。 移动核心可以部署在 VM、物理服务器或运营商的云中，无需专用硬件。

- 专用网络服务管理器 - Affirmed 网络：专用网络服务管理器是运营商用于部署、监视和管理 Azure 平台上的专用移动核心网络的应用程序。 它提供一整套管理功能，包括通过编程 GUI 驱动的门户对专用网络资源进行简单自激活和管理。

- Azure 网络功能管理器：Azure 网络功能管理器 (NFM) 是一种完全托管的云原生业务流程服务，确保客户在 Azure Stack Edge Pro GPU 上部署和预配网络功能，以使用 Azure 门户实现一致混合体验。

- Azure 云：一个公有云计算平台，其解决方案包括基础结构即服务 (IaaS)、平台即服务 (PaaS) 和软件即服务 (SaaS)，可用于分析、虚拟计算、存储、网络等服务。

- Azure Stack Edge：Microsoft 附带的云托管硬件即服务解决方案。 它将 Azure 云的能力引入本地和可靠服务器，几乎可以部署在执行本地 AI 和高级计算任务所需的任何位置。


:::image type="content" source="./media/affirmed-overview/affirmed-private-network-service-solution.png" alt-text="Affirmed 专用网络服务解决方案":::

## <a name="why-use-the-affirmed-private-network-solution"></a>为何使用 Affirmed 专用网络解决方案？
APNS 为运营商及其客户提供以下主要优势：

- 部署灵活性 - APNS 采用控制和用户平面分离技术，并支持三种类型的部署模式，满足运营商向企业提供的各种场景需求。 通过使用专用网络服务管理器，运营商可以配置以下部署模型：

    - 独立模型使运营商能够在本地提供完整的独立专用网络，具体做法是在 Azure Stack Edge 上提供 RAN、5G 核心，在集中式云上提供管理层。

    - 分布式模型通过将用户平面分布在 Azure Stack Edge 上更接近企业边缘的地方，将控制平面分布在云上，从而实现更快的数据处理；该模型的一个示例是制造设备。

    - “一切在云中”允许在 RAN 位于边缘时将整个 5G 核心部署到云中，从而动态分配云资源以满足工作负载不断变化的需求。

- MNO 集成 - APNS 集成了移动网络运营商，这意味着它使用分布式订阅者核心在专用和公共运营商网络中提供完整的移动性。 运营商的优势是将专用移动网络缩放到数千个企业边缘站点。

    - 支持所有 Spectrum 选项 - MNO 许可、专用许可、CBRS、共享、未授权。

    - 支持隔离/独立专用网络、多站点漫游和宏漫游，因为它集成了 MNO。

    - 可提供 99.999% 的服务可用性，并可以与任何符合 3GPP 的 LTE 和 5G NR 无线电交互运作。 对企业具有运营商级别复原能力。

- 自动化和易管理性 - 可以通过 Azure 云上的服务管理器完全远程管理 APNS 解决方案。 通过服务管理器，最终用户可以访问其个性化仪表板，并且可以在专用移动网络上管理、查看和打开/关闭设备。 运营商可以监视网络状态，确定网络问题和关键参数，以确保最佳性能。

    - 提供在 Azure 专用多接入边缘计算上运行的安全、可靠、高带宽、低延迟的专用移动网络服务。

    - 支持完整的远程管理，无需出车维修。

    - 提供云自动化，使运营商能够向企业提供托管服务，或与反过来可以提供托管服务的 MSP 合作。

- 更智能的网络和业务见解 - Affirmed 移动核心具有嵌入式虚拟探测/数据包中转功能，可用于提供网络见解。 运营商可以使用这些见解更好地推动网络决策，同时其客户可以使用这些见解来推动更智能的盈利决策。

- 数据隐私和安全性 - APNS 使用 Azure 跨专用网络和企业应用程序提供安全性和符合性。 运营商可以放心地为需要严格数据隐私法的行业用例（例如医疗保健、政府、公共安全和防御）部署解决方案。

## <a name="next-steps"></a>后续步骤
- 了解如何[部署 Affirmed 专用网络服务解决方案](deploy-affirmed-private-network-service-solution.md)



