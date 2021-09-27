---
title: Azure 安全基准基础蓝图示例概述
description: Azure 安全基准基础蓝图示例的概述和体系结构。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 82521251ca395a0d5a1409dae62954950b7da8a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614724"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Azure 安全基准基础蓝图示例的概述

Azure 安全基准基础蓝图示例提供一套基线基础结构模式来帮助你构建安全且合规的 Azure 环境。 此蓝图有助于部署基于云的体系结构，以便为有认证或合规性要求的方案提供解决方案。 它根据 [Azure 安全基准](../../../../security/benchmarks/index.yml)中定义的策略和其他规范措施部署和配置网络边界、监视和其他资源。

## <a name="architecture"></a>体系结构

此蓝图示例创建的基础环境基于[中心和分支模型](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)的体系结构原则。
此蓝图部署一个中心虚拟网络来托管其他/可选管理、维护、管理和连接基础结构，该虚拟网络包含常见的共享资源、服务和项目（例如用于连接、管理和跳转盒子网的 Azure Bastion、网关和防火墙）。 部署一个或多个分支虚拟网络来托管应用程序工作负载，例如 Web 和数据库服务。 分支虚拟网络使用 Azure 虚拟网络对等互连无缝并安全连接到中心虚拟网络。 其他分支可通过重新分配示例蓝图或手动创建 Azure 虚拟网络并将其与中心虚拟网络进行对等互连来添加。 配置分支虚拟网络和子网的所有外部连接，使其通过中心虚拟网络、防火墙、网关和管理跳转盒进行路由。

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Azure 安全基准基础蓝图示例体系结构图" border="false":::

此蓝图部署多个 Azure 服务来提供安全、受监视的企业就绪基础。 此环境包括：

- [Azure Monitor 日志](../../../../azure-monitor/logs/data-platform-logs.md)和 Azure 存储帐户，确保资源日志、活动日志、指标和网络流量流都存储在一个中心位置，以便轻松进行查询、分析、存档和预警。
- [Azure 安全中心](../../../../security-center/security-center-introduction.md)（标准版），用于为 Azure 资源提供威胁防护。
- 中心 [Azure 虚拟网络](../../../../virtual-network/virtual-networks-overview.md)，支持用于连接回本地网络的子网，Internet 连接的入口和出口堆栈以及用于部署其他管理服务的可选子网。 分支虚拟网络，包含用于托管应用程序工作负载的子网。 部署后可根据需要创建其他子网以便支持适用的场景。
- [Azure 防火墙](../../../../firewall/overview.md)，用于路由所有出站 Internet 流量以及通过跳转盒启用入站 Internet 流量。 （默认防火墙规则会阻止所有 Internet 入站和出站流量，因此如果适用，部署规则后必须进行配置。）
- 分配到所有子网的[网络安全组](../../../../virtual-network/network-security-group-how-it-works.md) (NSG)，配置这些子网是为了阻止所有 Internet 入站和出站流量，但服务拥有的子网（例如 Azure Bastion、网关和 Azure 防火墙）除外。
- [应用程序安全组](../../../../virtual-network/application-security-groups.md)，用于对 Azure 虚拟机进行分组以适应常用网络安全策略。
- [路由表](../../../../virtual-network/manage-route-table.md)，用于通过防火墙从子网路由所有出站 Internet 流量。 （Azure 防火墙和 NSG 规则需要在部署之后进行配置才能打开连接。）
- [Azure 网络观察程序](../../../../network-watcher/network-watcher-monitoring-overview.md)，用于监视、诊断和查看 Azure 虚拟网络中的资源指标。
- [Azure DDoS 防护标准](../../../../ddos-protection/ddos-protection-overview.md)用于防止 Azure 资源受到 DDoS 攻击。
- [Azure Bastion](../../../../bastion/bastion-overview.md)，用于无缝并安全连接到虚拟机，不需要公共 IP 地址、代理或特殊客户端软件。
- [Azure VPN 网关](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md)，用于通过公共 Internet 在 Azure 虚拟网络和本地位置之间启用加密流量。

> [!NOTE]
> Azure 安全基准基础布设了用于工作负载的基础体系结构。 上面的体系结构图包括几种名义上的资源，以便展示潜在的子网用途。 你仍需要部署此基础体系结构上的工作负载。

## <a name="next-steps"></a>后续步骤

你已查看了 Azure 安全基准基础蓝图示例的概述和体系结构。

> [!div class="nextstepaction"]
> [Azure 安全基准基础蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
