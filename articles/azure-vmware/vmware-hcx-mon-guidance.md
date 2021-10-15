---
title: VMware HCX 移动优化网络 (MON) 指南
description: 了解适用于移动优化网络 (MON) 的 Azure VMware 解决方案特定用例。
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 1b170615b5a0ec0df81a8be7c71b8ac5ae17a24a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424722"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>VMware HCX 移动优化网络 (MON) 指南

>[!IMPORTANT] 
>在启用 HCX MON 之前，请阅读以下限制和不支持的配置：
>
>[HCX NE 不支持的源配置](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[任何 HCX 部署（包括 MON）的限制](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[HCX 移动优化网络 (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) 是在使用 [HCX 网络扩展 (NE)](configure-hcx-network-extension.md) 时可以选择启用的一项功能。 MON 可在某些情况下提供理想的流量路由，以防止在扩展网络上的本地和基于云的资源之间发生网络转接。 

在整个迁移周期中，MON 针对以下情况优化应用程序移动：

- 使用扩展网络时，针对虚拟机 (VM) 到 VM L2 的通信进行优化 

- 优化和避免本地、Azure VMware 解决方案和 Azure 之间的非对称流量流


本文介绍适用于 MON 的 Azure VMware 解决方案特定用例。


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>在私有云端跨标准和延伸段优化流量流 

在本场景中，使用 NE 将 VM1 迁移到云，从而实现理想的 VM 到 VM 延迟。 因此，在本地 Azure VMware 解决方案分段上，VM1 需要对 VM3 具有较低的延迟。 我们会将 VM1 网关从本地迁移到 Azure VMware 解决方案（云），以确保流量（蓝线）获得最佳路径。 如果网关仍在本地（红线），则会观察到转接效果和更高的延迟。 

>[!NOTE]
>在不将 VM 网关迁移到云端的情况下启用 MON 时，无法确保流量流获得最佳路径。  它也不允许评估策略路由。

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="显示使用延伸网络时 VM 到 VM L2 通信优化的示意图。" border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>优化和避免非对称流量流 

在本场景中，我们假设本地 VM 已迁移到 Azure VMware 解决方案并参与 L2，而 L3 流量流回到本地以访问服务。 此外，我们还假设某些来自 Azure（Azure VMware 解决方案连接的 vNET 中）的 VM 通信可以进入 Azure VMware 解决方案私有云。

>[!IMPORTANT]
>此处的要点是仔细规划和避免非对称流量流。 

默认情况下，在不使用 MON 时，Azure VMware 解决方案中的 VM 在扩展网络上没有 MON 时，可以使用 ExpressRoute 首选路径与本地进行通信。 理想情况下，基于客户用例，应评估如何通过 ExpressRoute 使用 NE 或 T0 网关，将启用了 MON 的 Azure VMware 解决方案延伸段上的 VM 遍历回本地，但使流量保持对称流动。

例如，如果选择了 NE 路径，则 MON 策略路由必须专门对本地端的子网进行寻址；否则，将使用 0.0/0 路由。 在 NE 段下选择“高级”即可找到策略路由。 默认情况下，所有 RFC1918 IP 地址都包含在 MON 策略路由定义中。 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="显示默认的基于策略的路由的屏幕截图。":::

仅当 VM 网关迁移到云时，才会评估策略路由。 此配置的作用是，目标的所有匹配子网通过 NE 设备隧道传输。  如果没有匹配项，则会通过 T0 网关进行路由。

>[!NOTE]
>在 Azure VMware 解决方案中使用 MON 的特别注意事项是将通过 BGP 播发的 /32 路由提供给其对等节点；这包括本地连接和 ExpressRoute 上的 Azure 连接。 例如，Azure 中的 VM 在启用了 MON 的 Azure VMware 解决方案段上了解 Azure VMware 解决方案 VM 的路径。 返回流量按预期发送回 T0 后，如果返回子网是 RFC1918 匹配项，则会强制流量通过 NE 路径而不是 T0 路径。  然后，在本地端通过 ExpressRoute 流回 Azure。  这可能会在中间和非对称路由行为中导致有状态防火墙产生混淆。 另外，最好确定 NE MON 段上的 VM 如何访问 Internet，是通过 Azure VMware 解决方案中的 T0，还是仅通过 NE 返回本地。

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="显示 RFC1918 出口和出口流量的示意图。" border="false":::

如上图所述，重点在于为每个所需子网匹配策略路由。 否则，流量将通过 T0（而不是 NE）进行路由。

 
若要了解有关策略路由的详细信息，请参阅[移动优化网络策略路由](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html)。

