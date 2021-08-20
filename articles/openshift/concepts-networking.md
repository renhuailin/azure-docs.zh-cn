---
title: 概念 - OpenShift 4 上的 Azure Red Hat 的网络图
description: Azure Red Hat OpenShift 网络图及概述
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 2e1825f80b5b2b185d5cf9d9ea75ea4e80ea0ef9
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653449"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) 的网络概念

本指南概述 OpenShift 4 群集上的 Azure Red Hat OpenShift 中的网络，并提供重要终结点的图示和列表。 若要详细了解核心 OpenShift 网路概念，请参阅 [Azure Red Hat OpenShift 4 网络文档](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)。

![Azure Red Hat OpenShift 4 网络示意图](./media/concepts-networking/aro4-networking-diagram.png)

部署 OpenShift 4 上的 Azure Red Hat 时，整个群集都包含在虚拟网络中。 在此虚拟网络中，主节点和工作器节点都位于各自的子网中。 每个子网都使用一个内部负载均衡器和一个公共负载均衡器。

## <a name="networking-components"></a>联网组件

以下列表涵盖 Azure Red Hat OpenShift 群集中的重要网络组件。

* **aro-pls**
    * 这是一个 Azure 专用链接终结点，由 Microsoft 和 Red Hat 站点的可靠性工程师用于管理群集。
* **aro-internal-lb**
    * 此终结点对流往 api 服务器的流量进行均衡。 对于此负载均衡器，主节点位于后端池中。
* **aro-public-lb**
    * 如果是公共 api，此终结点会将流量路由到 api 服务器并对其进行均衡。 此终结点分配一个公共传出 IP，以便主节点可以访问 Azure 资源管理器并发回有关群集运行状况的报告。
* **aro-internal**
    * 此终结点均衡内部服务流量。 对于此负载均衡器，工作器节点位于后端池中。
    * 默认情况下不创建此负载均衡器。 创建具有正确注释的 LoadBalancer 类型的服务后，将随即创建此负载均衡器。 例如：service.beta.kubernetes.io/azure-load-balancer-internal: "true"。
* **aro-internal-lb**
    * 此终结点用于公共流量。 创建应用程序和路由时，这是入口流量的路径。
    * 此负载均衡器还通过 Azure 负载均衡器出站规则应用于来自工作器节点中运行的任何 Pod 的出口 Internet 连接。
        * 当前出站规则不可配置。 这些规则为每个节点分配 1,024 个 TCP 端口。
        * LB 规则中未配置 DisableOutboundSnat，因此 Pod 可以获取此 ALB 中配置的任何公共 IP 作为出口 IP。
        * 作为前两点的结果，添加临时 SNAT 端口的唯一方法就是将公共 LoadBalanceer 类型的服务添加到 ARO。
* **aro-outbound-pip**
    * 此终结点充当工作器节点的公共 IP (PIP)。
    * 此终结点使服务能够将来自 Azure Red Hat OpenShift 群集的特定 IP 添加到允许列表。
* **aro-nsg**
    * 公开服务时，api 将在此网络安全组中创建一个规则，以便流量流经并到达控制平面和节点。
    * 默认情况下，此网络安全组允许所有出站流量。 目前，只能将出站流量限制到 Azure Red Hat OpenShift 控制平面。
* **aro-controlplane-nsg**
  * 此终结点只允许流量通过主节点的端口 6443 进入。
* **Azure 容器注册表**
    * 这是一个由 Microsoft 在内部提供和使用的容器注册表。 此注册表为只读，不供 Azure Red Hat OpenShift 用户使用。
        * 此注册表提供主机平台映像和群集组件。 例如监视用或记录用容器。
        * 与此注册表的连接是经由服务终结点建立的（Azure 服务之间的内部连接）。
        * 默认情况下，此内部注册表在群集之外不可用。
* **专用链接**
    * 允许从管理平面到群集的网络连接，以便 Microsoft 和 Red Hat 站点的可靠性工程师帮助管理群集。

## <a name="networking-policies"></a>网络策略

* **流入量**：流入量网络策略作为 [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html) 的一部分受到支持。 默认启用此网络策略，由用户执行。 尽管流入量网络策略符合 V1 NetworkPolicy，但不支持 Egress 和 IPBlock 类型。

* **流出量**：通过使用 OpenShift 中的 [出口防火墙](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html)功能来支持流出量网络策略。 每个命名空间/项目只有一个流出量策略。 流出量策略不受“默认”命名空间支持，并且是按顺序（从第一个到最后一个）进行评估的。

## <a name="networking-basics-in-openshift"></a>OpenShift 中的网络基础知识

OpenShift 软件定义的网络 [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) 用于通过 Open vSwitch [(OVS)](https://www.openvswitch.org/) 配置叠加网络，这是一种基于容器网络接口 (CNI) 规范的 OpenFlow 实现。 SDN 支持不同的插件，网络策略是在 OpenShift 4 上的 Azure Red Hat 中使用的插件。 所有网络通信都由 SDN 管理，因此虚拟网络上不需要额外的路由来实现 Pod 到 Pod 通信。

## <a name="networking--for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的网络

以下网络功能特定于 Azure Red Hat OpenShift：  
* 用户可以在现有虚拟网络中创建其 ARO 群集，也可以在创建其 ARO 群集时创建虚拟网络。
* Pod 和服务网络 CIDR 是可配置的。
* 节点和主节点位于不同的子网中。
* 节点和主节点虚拟网络子网的最小值应设为 /27。
* 默认 Pod CIDR 为 10.128.0.0/14。
* 默认服务 CIDR 为 172.30.0.0/16。
* Pod 和服务网络 CIDR 不应当与网络上使用的其他地址范围重叠，并且不得在群集的虚拟网络 IP 地址范围内。
* Pod CIDR 的最小值应设为 /18。 （Pod 网络是不可路由的 IP，并且仅在 OpenShift SDN 内使用。）
* 每个节点都为其 Pod 分配 /23 子网 （512 个 IP）。 此值不能更改。
* 不能将 Pod 附加到多个网络。
* 不能配置出口静态 IP。 （这是一种 OpenShift 功能。 有关信息，请参阅[配置出口 IP](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)）。

## <a name="network-settings"></a>网络设置

以下网络设置适用于 Azure Red Hat OpenShift 4 群集：

* “API 可见性”- 设置当运行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 时的 API 可见性。
    * “公共”- 外部网络可访问 API 服务器。
    * “专用”- 从主节点子网向 API 服务器分配了专用 IP，只能使用连接的网络（对等虚拟网络、群集中的其他子网）访问该服务器。 将代表客户创建专用 DNS 区域。
* “入口可见性”- 设置当运行 [az aro create command](tutorial-create-cluster.md#create-the-cluster) 时的 API 可见性。
    * “公共”路由将默认为公共标准负载均衡器（可以更改）。
    * “专用”路由将默认为内部负载均衡器（可以更改）。

## <a name="network-security-groups"></a>网络安全组
在节点的资源组中创建网络安全组，该资源组已向用户锁定。 直接向不在节点 NIC 上的子网分配网络安全组。 网络安全组是不可变的，并且用户没有更改它们的权限。

如果 API 服务器公开可见，则无法创建网络安全组并将其分配给 NIC。

## <a name="domain-forwarding"></a>域转发
Azure Red Hat OpenShift 使用 CoreDNS。 可以配置域转发。 你无法将自己的 DNS 引入虚拟网络。 有关详细信息，请参阅关于如何[使用 DNS 转发](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator)的文档。

## <a name="whats-new-in-openshift-45"></a>OpenShift 4.5 中的新增内容

在 OpenShift 4.5 的支持下，Azure Red Hat OpenShift 进行了几个重要的体系结构更改。 这些更改仅适用于新建的运行 OpenShift 4.5 的群集。 在升级过程中，将不会更改已升级到 OpenShift 4.5 的现有群集的网络体系结构。 用户需要重新创建其群集才能使用这一新体系结构。

![Azure Red Hat OpenShift 4.5 网络示意图](./media/concepts-networking/aro-4-5-networking-diagram.png)

如上图所示，你会注意到一些变化：
* 之前，ARO 使用了两个公共负载均衡器：一个用于 API 服务器，另一个用于工作器节点池。 此体系结构更新后，这两个负载均衡器已合并到一个负载均衡器中。 
* 为降低复杂性，已删除专用外部 IP 地址资源。
* ARO 控制平面现在与 ARO 工作器节点共享一个网络安全组。

有关 OpenShift 4.5 的详细信息，请查看 [OpenShift 4.5 发行说明](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)。

## <a name="next-steps"></a>后续步骤
详细了解出站流量和 Azure Red Hat OpenShift 的出口支持，请参阅[支持策略](support-policies-v4.md)文档。
