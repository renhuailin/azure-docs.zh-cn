---
title: 在 Azure VMware 解决方案中部署 Horizon
description: 了解如何在 Azure VMware 解决方案中部署 VMware Horizon。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: d99c8dc76dcab1866d0c536be7fc505c2eec0cb6
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323643"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>在 Azure VMware 解决方案中部署 Horizon 

>[!NOTE]
>本文档重点介绍 VMware Horizon 产品（以前称为 Horizon 7）。 Horizon 是不同于 Horizon Cloud on Azure 的解决方案，不过两者共享一些组件。 Azure VMware 解决方案的主要优势包括：更直接的大小调整方法、在 Azure 门户中集成了 VMware Cloud Foundation 管理。

[VMware Horizon](https://www.vmware.com/products/horizon.html)® 虚拟桌面和应用程序平台在数据中心内运行，提供简单的集中式管理。 它在任何位置的任何设备上提供虚拟桌面和应用程序。 使用 Horizon 可以创建和中转与 Windows 和 Linux 虚拟桌面、远程桌面服务器 (RDS) 托管应用程序、桌面和物理计算机的连接。

本文专门介绍如何在 Azure VMware 解决方案中部署 Horizon。 有关 VMware Horizon 的一般信息，请参阅 Horizon 产品文档：

* [什么是 VMware Horizon？](https://www.vmware.com/products/horizon.html)

* [详细了解 VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizon 参考体系结构](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

随着 Horizon 在 Azure VMware 解决方案中的引入，现在 Azure 平台上有两款虚拟桌面基础结构 (VDI) 解决方案。 下图概要汇总了主要差异。

:::image type="content" source="media/vmware-horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="显示 Azure VMware 解决方案中的 Horizon 与 Horizon Cloud on Azure 之间的差异的图。" border="false":::

Horizon 8 发行系列中的 Horizon 2006 及更高版本支持本地部署和 Azure VMware 解决方案部署。 有少量的 Horizon 功能在本地受支持，但在 Azure VMware 解决方案中不受支持。 Horizon 生态系统中的其他产品也受支持。 有关详细信息，请参阅[功能奇偶一致性和互操作性](https://kb.vmware.com/s/article/80850)。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>在混合云中部署 Horizon

使用 Horizon Cloud Pod 体系结构 (CPA) 来互连本地数据中心和 Azure 数据中心时，可以在混合云环境中部署 Horizon。 CPA 将纵向扩展部署，构建混合云，并提供冗余来实现业务连续性和灾难恢复。  有关详细信息，请参阅[扩展现有的 Horizon 7 环境](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)。

>[!IMPORTANT]
>CPA 不是延伸的部署；每个 Horizon Pod 都是不同的，属于每一单个 Pod 的所有 Connection Server 都需要位于单个位置，并且从网络角度看，它们需要在同一广播域中运行。

与本地数据中心或专用数据中心一样，可以在 Azure VMware 解决方案私有云中部署 Horizon。 以下部分将讨论在本地部署 Horizon 与在 Azure VMware 解决方案中部署 Horizon 的主要差异。

Azure 私有云在概念上与 VMware SDDC 相同，后者是 Horizon 文档中常用的术语 。 本文档的其余部分交换使用这两个术语。

Azure VMware 解决方案中的 Horizon 必须使用 Horizon Cloud Connector 来管理订阅许可证。 可将 Cloud Connector 连同 Horizon Connection Server 一起部署在 Azure 虚拟网络中。

>[!IMPORTANT]
>目前还没有为 Azure VMware 解决方案中的 Horizon 提供 Horizon 控制平面支持。 请确保下载 VHD 版本的 Horizon Cloud Connector。

## <a name="vcenter-cloud-admin-role"></a>vCenter 云管理员角色

由于 Azure VMware 解决方案是一个 SDDC 服务，而 Azure 会管理 Azure VMware 解决方案中的 SDDC 的生命周期，因此在设计上限制了 Azure VMware 解决方案中的 vCenter 权限模型。

客户需要使用云管理员角色，该角色拥有受限的一组 vCenter 权限。 Horizon 产品已经过修改，可以与 Azure VMware 解决方案中的云管理员角色配合使用，具体如下所述：

* 即时克隆预配已经过修改，可以在 Azure VMware 解决方案中运行。

* 在 Azure VMware 解决方案中创建了一个与 Horizon 配合使用的特定 vSAN 策略 (VMware_Horizon)，在为 Horizon 部署的 SDDC 中必须提供并使用该策略。

* vSphere 基于内容的读取缓存 (CBRC)（也称为视图存储加速器）在 Azure VMware 解决方案中运行时将会被禁用。

>[!IMPORTANT]
>不得重新启用 CBRC。

>[!NOTE]
>只要你在 Horizon 8 分支上部署 Horizon 2006（也称为 Horizon 8）及更高版本并在 Horizon Connection Server 安装程序中选择“Azure”选项，Azure VMware 解决方案就会自动配置特定的 Horizon 设置。

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware 解决方案中的 Horizon 部署体系结构

典型的 Horizon 体系结构设计使用 Pod 和块策略。 块是单个 vCenter，而多个块组合在一起就构成了一个 Pod。 Horizon Pod 是按 Horizon 可伸缩性限制确定的组织单位。 每个 Horizon Pod 具有单独的管理门户，因此标准的设计实践是尽量减少 Pod 数量。

每个云具有自身的网络连接方案。 与 VMware SDDC 网络/NSX Edge 相结合，通过 Azure VMware 解决方案网络连接部署 Horizon 时，需要满足不同于本地部署的独特要求。

在做出以下假设的情况下，每个 Azure 私有云和 SDDC 可以处理 4,000 个桌面会话或应用程序会话：

* 工作负载流量符合 LoginVSI 任务辅助角色配置文件。

* 仅考虑协议流量，而不考虑用户数据。

* NSX Edge 已配置为大型实例。

>[!NOTE]
>工作负载配置文件和需求可能不同，因此结果可能因用例而异。 在工作负载的上下文中，用户数据卷可能会降低规模限制。 请相应地规划你的部署并调整其规模。 有关详细信息，请参阅[调整 Horizon 部署的 Azure VMware 解决方案主机大小](#size-azure-vmware-solution-hosts-for-horizon-deployments)部分中的大小调整指导原则。

考虑到 Azure 私有云和 SDDC 的最大限制，我们建议采用一种在 Azure 虚拟网络中运行 Horizon Connection Server 和 VMware Unified Access Gateway (UAG) 的部署体系结构。 这可以有效地将每个 Azure 私有云和 SDDC 转化为块， 从而可以最大程度地提高 Azure VMware 解决方案中运行的 Horizon 的可伸缩性。

从 Azure 虚拟网络到 Azure 私有云/SDDC 的连接应该配置有 ExpressRoute FastPath。 下图显示了基本的 Horizon Pod 部署。

:::image type="content" source="media/vmware-horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="显示使用 ExpressPath Fast Path 的典型 Horizon Pod 部署的图。" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>用于缩放 Azure VMware 解决方案中的 Horizon 的网络连接

本部分概要介绍网络体系结构，并提供一些常见部署示例来帮助你缩放 Azure VMware 解决方案中的 Horizon。 本部分专门重点介绍了关键的网络元素。 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware 解决方案中的单个 Horizon Pod

:::image type="content" source="media/vmware-horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="显示 Azure VMware 解决方案中的单个 Horizon Pod 的图。" border="false":::

单个 Horizon Pod 是最直接的部署方案，因为只需在“美国东部”区域部署一个 Horizon Pod。  由于每个私有云和 SDDC 预计可处理 4,000 个桌面会话，因此可以部署最大的 Horizon Pod 大小。  可以规划最多包含三个私有云/SDDC 的部署。

在 Azure 虚拟网络中部署 Horizon 基础结构虚拟机 (VM) 后，为每个 Horizon Pod 处理的会话数可以达到 12,000 个。 每个私有云和 SDDC 与 Azure 虚拟网络之间的连接是 ExpressRoute Fast Path。  不需要私有云之间的东西流量。 

此基本部署示例的重要假设包括：

* 你不想要使用云 Pod 体系结构 (CPA) 将本地 Horizon Pod 连接到此新 Pod。

* 最终用户通过 Internet 连接到其虚拟桌面（而不是通过本地数据中心连接）。

通过 VPN 或 ExpressRoute 线路将 Azure 虚拟网络中的 AD 域控制器连接到本地 AD。

可对该基本示例做出的一项改变是支持本地资源的连接。 例如，用户需要访问桌面并生成虚拟桌面应用程序流量，或使用 CPA 连接到本地 Horizon Pod。

下图显示了如何支持本地资源的连接。 若要将企业网络连接到 Azure 虚拟网络，需要使用 ExpressRoute 线路。  此外，还需要使用 ExpressRoute Global Reach 将企业网络连接到每个私有云和 SDDC。  这样，便可以从 SDDC 连接到 ExpressRoute 线路和本地资源。 

:::image type="content" source="media/vmware-horizon/connect-corporate-network-azure-virtual-network.png" alt-text="显示企业网络到 Azure 虚拟网络的连接的图。" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Azure VMware 解决方案中跨多个区域的多个 Horizon Pod

另一种方案是跨多个 Pod 扩展 Horizon。  在此方案中，你将在两个不同的区域中部署两个 Horizon Pod，并使用 CPA 将它们相联合。 此方案的网络配置与前一个示例类似，不过需要额外添加一些跨区域链接。 

将每个区域中的 Azure 虚拟网络连接到另一区域中的私有云/SDDC。 这样，属于 CPA 联合的 Horizon 连接服务器便可以连接到受管理的所有桌面。 将额外私有云/SDDC 添加到此配置总共可以扩展到 24,000 个会话。 

如果在同一区域中部署两个 Horizon Pod，上述原则同样适用。  请务必在单独的 Azure 虚拟网络中部署第二个 Horizon Pod。 与单个 Pod 示例一样，可以使用 ExpressRoute 和 Global Reach 将企业网络与本地 Pod 连接到此多 Pod/区域示例。 

:::image type="content" source="media/vmware-horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="显示 Azure VMware 解决方案中跨多个区域的多个 Horizon Pod 的图。" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>调整 Horizon 部署的 Azure VMware 解决方案主机大小 

相比调整本地 Horizon 中的主机大小，Horizon 对 Azure VMware 解决方案中运行的主机采用的大小调整方法更简单。  这是因为 Azure VMware 解决方案主机已标准化。  精确的主机大小调整有助于确定支持 VDI 要求所需的主机数。  它对确定每个桌面的成本起着支配性的作用。

### <a name="sizing-tables"></a>调整表大小

Horizon 虚拟桌面的具体 vCPU/vRAM 要求取决于客户的具体工作负载配置文件。   请在 MSFT 和 VMware 销售团队的帮助下确定虚拟桌面的 vCPU/vRAM 要求。 

| 每个 VM 的 vCPU 数 | 每个 VM 的 vRAM (GB) | 实例 | 100 个 VM | 200 个 VM | 300 个 VM | 400 个 VM | 500 个 VM | 600 个 VM | 700 个 VM | 800 个 VM | 900 个 VM | 1000 个 VM | 2000 个 VM | 3000 个 VM | 4000 个 VM | 5000 个 VM | 6000 个 VM | 6400 个 VM |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Horizon 大小调整输入

下面是需要为规划的工作负载收集的信息：

* 并发桌面数

* 每个桌面所需的 vCPU 数

* 每个桌面所需的 vRAM

* 每个桌面所需的存储

一般情况下，VDI 部署要么受 CPU 限制，要么受 RAM 限制，这种限制决定了主机大小。 让我们分析以下已使用性能测试进行验证的、LoginVSI 知识工作者类型的工作负载示例：

* 包含 2,000 个并发桌面的部署

* 每个桌面 2 个 vCPU。

* 每个桌面 4 GB vRAM。

* 每个桌面 50 GB 存储

在此示例中，主机总数在因式分解后为 18，因此得出每个主机的 VM 密度为 111。

>[!IMPORTANT]
>客户工作负载与此 LoginVSI 知识工作者示例不同。 在规划部署的过程中，请与 VMware EUC SE 协作以满足具体的大小调整和性能需求。 在最终确定主机大小并相应地做出调整之前，请务必使用实际的已规划好的工作负载运行性能测试。

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware 解决方案中的 Horizon 许可 

在 Azure VMware 解决方案中运行 Horizon 的总成本由四个部分组成。 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware 解决方案容量成本

有关定价的信息，请参阅 [Azure VMware 解决方案定价](https://azure.microsoft.com/pricing/details/azure-vmware/)页

### <a name="horizon-licensing-cost"></a>Horizon 许可成本

在 Azure VMware 解决方案中可以使用两种许可证，其用户可以是并发用户 (CCU) 或命名用户 (NU)：

* Horizon 订阅许可证

* Horizon 通用订阅许可证

如果只是要在将来可预见的一段时间在 Azure VMware 解决方案中部署 Horizon，请使用 Horizon 订阅许可证，因为它的成本更低。

如果要在 Azure VMware 解决方案中和本地部署，请选择 Horizon 通用订阅许可证（就像在灾难恢复用例中一样）。 但是，此许可证包括用于本地部署的 vSphere 许可证，因此成本更高。

与 VMware EUC 销售团队协作，根据需求确定 Horizon 许可成本。

### <a name="azure-instance-types"></a>Azure 实例类型

若要了解 Horizon 基础结构所需的 Azure 虚拟机大小，请参阅 [Azure VMware 解决方案中的 Horizon 安装](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)。

## <a name="references"></a>参考
[Horizon Agent for Linux 系统要求](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>后续步骤
若要详细了解 Azure VMware 解决方案中的 VMware Horizon，请参阅 [VMware Horizon 常见问题解答](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)。
