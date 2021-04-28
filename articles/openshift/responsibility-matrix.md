---
title: Azure Red Hat OpenShift 责任分配矩阵
description: 了解 Azure Red Hat OpenShift 群集操作的责任所有权
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, support
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537003"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的责任概述

本文档概述 Microsoft、Red Hat 和 Azure Red Hat OpenShift 群集客户的责任。 有关 Azure Red Hat OpenShift 及其组件的详细信息，请参阅 Azure Red Hat OpenShift 服务定义。

虽然 Microsoft 和 Red Hat 会管理 Azure Red Hat OpenShift 服务，但客户同样承担与群集功能相关的责任。 虽然 Azure Red Hat OpenShift 群集托管在客户 Azure 订阅中的 Azure 资源上，但这些群集以远程方式访问。 基础平台和数据安全由 Microsoft 和 Red Hat 负责。

## <a name="overview"></a>概述
<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">事件和操作管理</a></strong>
   </td>
   <td><strong><a href="#change-management">变更管理</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">标识和访问管理</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">安全性和法规遵从性</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">客户数据</a>
   </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">客户应用程序</a>
   </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">开发人员服务</a>
   </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
   <td>客户 </td>
  </tr>
  <tr>
   <td>平台监视 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>日志记录 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>共享 </td>
  </tr>
  <tr>
   <td>应用程序网络 </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>群集网络 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>虚拟网络 </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>共享 </td>
   <td>共享 </td>
  </tr>
  <tr>
   <td>控制平面节点 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>辅助角色节点 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>群集版本 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>共享 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>容量管理 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>共享 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>虚拟存储 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
  <tr>
   <td>物理基础结构和安全性 </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
   <td>Microsoft 和 Red Hat </td>
  </tr>
</table>


表 1. 按资源划分的责任


## <a name="tasks-for-shared-responsibilities-by-area"></a>按区域划分的责任分担任务 

### <a name="incident-and-operations-management"></a>事件和操作管理 

客户、Microsoft 和 Red Hat 分担 Azure Red Hat OpenShift 群集的监视和维护责任。 客户负责[客户应用程序数据](#customer-data-and-applications)的事件和操作管理，以及客户可能配置的任何自定义网络。

<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong>Microsoft 和 Red Hat 责任</strong>
   </td>
   <td><strong>客户责任</strong>
   </td>
  </tr>
  <tr>
   <td>应用程序网络 </td>
   <td>
<ul>

<li>监视云负载均衡器和本机 OpenShift 路由器服务，并对警报做出响应。
</li>
</ul>
   </td>
   <td>
<ul>

<li>监视服务负载均衡器终结点的运行状况。

<li>监视应用程序路由的运行状况及其后面的终结点。

<li>向 Microsoft 和 Red Hat 报告故障。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>虚拟网络
   </td>
   <td>
<ul>

<li>监视默认平台网络所需的云负载均衡器、子网和 Azure 云组件，并对警报做出响应。
</li>
</ul>
   </td>
   <td>
<ul>

<li>监视可选择通过 VNet 到 VNet 连接、VPN 连接或专用链接连接进行配置的网络流量，以发现潜在问题或安全威胁。
</li>
</ul>
   </td>
  </tr>
</table>


表 2. 事件和操作管理的责任分担


### <a name="change-management"></a>更改管理

Microsoft 和 Red Hat 负责实现对客户将控制的群集基础结构和服务的更改，以及维护可用于主节点、基础结构服务和工作器节点的版本。 客户负责发起基础结构更改、安装和维护群集上的可选服务和网络配置，以及对客户数据和客户应用程序的所有更改。


<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong>Microsoft 和 Red Hat 责任</strong>
   </td>
   <td><strong>客户责任</strong>
   </td>
  </tr>
  <tr>
   <td>日志记录 </td>
   <td>
<ul>

<li>集中聚合和监视平台审核日志。

<li>为客户提供文档，以使用 Log Analytics 通过适用于容器的 Azure Monitor 实现应用程序日志记录。

<li>根据客户请求提供审核日志。
</li>
</ul>
   </td>
   <td>
<ul>

<li>在群集上安装可选的默认应用程序日志记录 Operator。

<li>安装、配置和维护任何可选的应用日志记录解决方案，例如日志记录挎斗容器或第三方日志记录应用程序。

<li>调整客户应用程序在影响群集稳定性时产生的应用程序日志的大小和频率。

<li>通过支持案例请求平台审核日志来研究特定事件。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>应用程序网络
   </td>
   <td>
<ul>

<li>设置公有云负载均衡器

<li>设置本机 OpenShift 路由器服务。 提供将路由器设置为专用并添加最多一个额外路由器分片的功能。

<li>安装、配置和维护用于默认内部 Pod 流量的 OpenShift SDN 组件。
</li>
</ul>
   </td>
   <td>
<ul>

<li>使用 NetworkPolicy 对象为项目和 Pod 网络、Pod 入口和 pod 出口配置非默认 Pod 网络权限。

<li>为特定服务请求和配置任何附加服务负载均衡器。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>群集网络
   </td>
   <td>
<ul>

<li>设置群集管理组件，如公共或专用服务终结点，以及与虚拟网络组件的必要集成。

<li>设置工作器节点和主节点之间的内部群集通信所需的内部网络组件。
</li>
</ul>
   </td>
   <td>
<ul>

<li>预配群集时通过 OpenShift 群集管理器提供计算机 CIDR、服务 CIDR 和 Pod CIDR 的可选非默认 IP 地址范围（如果需要）。

<li>通过 Azure CLI 请求在创建群集时或创建群集后将 API 服务终结点设为公共或专用。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>虚拟网络
   </td>
   <td>
<ul>

<li>设置和配置预配群集所需的虚拟网络组件，包括虚拟私有云、子网、负载均衡器、Internet 网关、NAT 网关等。

<li>让客户能够通过 OpenShift 群集管理器根据需要管理与本地资源的 VPN 连接、VNet 到 VNet 连接以及专用链接连接。

<li>使客户能够创建和部署用于服务负载均衡器的公有云负载均衡器。
</li>
</ul>
   </td>
   <td>
<ul>

<li>设置并维护可选的公有云网络组件，例如 VNet 到 VNet 连接、VPN 连接或专用链接连接。

<li>为特定服务请求和配置任何附加服务负载均衡器。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>群集版本
   </td>
   <td>
<ul>

<li>传达次要和维护版本的升级计划和状态

<li>发布次要和维护升级的更改日志和发行说明
</li>
</ul>
   </td>
   <td>
<ul>

<li>启动群集升级

<li>测试次要和维护版本的客户应用程序以确保兼容性
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>容量管理
   </td>
   <td>
<ul>

<li>监视控制平面（主节点）的利用率

<li>缩放和/或调整控制平面节点的大小以维护服务质量

<li>监视客户资源（包括网络、存储和计算容量）的利用率。 如果客户未启用自动缩放功能，通知客户进行群集资源所需的任何更改（例如， 要缩放的新计算节点、额外的存储等）
</li>
</ul>
   </td>
   <td>
<ul>

<li>根据需要使用提供的 OpenShift 群集管理器控件来新增或删除额外的工作器节点。

<li>对有关群集资源要求的 Microsoft 和 Red Hat 通知做出响应。
</li>
</ul>
   </td>
  </tr>
</table>


表 3. 更改管理的责任分担


### <a name="identity-and-access-management"></a>标识和访问管理 

标识和访问管理包括用于确保只有适当的人员才能访问群集、应用程序和基础结构资源的所有相关责任。 这包括一些任务，如提供访问控制机制、身份验证、授权和管理资源访问权限。


<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong>Microsoft 和 Red Hat 责任</strong>
   </td>
   <td><strong>客户责任</strong>
   </td>
  </tr>
  <tr>
   <td>日志记录 </td>
   <td>
<ul>

<li>遵循适用于平台审核日志的基于行业标准的分层内部访问过程。

<li>提供本机 OpenShift RBAC 功能。
</li>
</ul>
   </td>
   <td>
<ul>

<li>配置 OpenShift RBAC 以控制对项目和项目的应用程序日志的访问。

<li>对于第三方或自定义应用程序日志记录解决方案，客户负责访问管理。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>应用程序网络
   </td>
   <td>
<ul>

<li>提供本机 OpenShift RBAC 功能。
</li>
</ul>
   </td>
   <td>
<ul>

<li>根据需要配置 OpenShift RBAC 以控制对路由配置的访问。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>群集网络
   </td>
   <td>
<ul>

<li>提供本机 OpenShift RBAC 功能。
</li>
</ul>
   </td>
   <td>
<ul>

<li>管理 Red Hat 帐户的 Red Hat 组织成员身份。

<li>管理 Red Hat 组织的组织管理员，以向 OpenShift 群集管理器授予访问权限。

<li>根据需要配置 OpenShift RBAC 以控制对路由配置的访问。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>虚拟网络
   </td>
   <td>
<ul>

<li>通过 OpenShift 群集管理器提供客户访问控制。
</li>
</ul>
   </td>
   <td>
<ul>

<li>通过 OpenShift 群集管理器管理对公有云组件的可选用户访问权限。
</li>
</ul>
   </td>
  </tr>
</table>


表 4. 标识和访问管理的责任分担


### <a name="security-and-regulation-compliance"></a>安全性和法规遵从性 

安全性和合规性包括用于确保遵守相关法律、政策和法规的任何责任和控制措施。


<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong>Microsoft 和 Red Hat 责任</strong>
   </td>
   <td><strong>客户责任</strong>
   </td>
  </tr>
  <tr>
   <td>日志记录 </td>
   <td>
<ul>

<li>将群集审核日志发送到 Microsoft 和 Red Hat SIEM 以分析安全事件。 将审核日志保留定义的时间段以支持取证分析。
</li>
</ul>
   </td>
   <td>
<ul>

<li>分析应用程序日志中的安全事件。 如果需要比默认日志记录堆栈提供的保留期更长，请通过日志记录挎斗容器或第三方日志记录应用程序将应用程序日志发送到外部终结点。
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>虚拟网络
   </td>
   <td>
<ul>

<li>监视虚拟网络组件是否存在潜在问题和安全威胁。

<li>使用其他公共 Microsoft 和 Red Hat Azure 工具进行额外的监视和保护。
</li>
</ul>
   </td>
   <td>
<ul>

<li>监视选择性配置的虚拟网络组件是否存在潜在问题和安全威胁。

<li>根据需要配置任何所需的防火墙规则或数据中心保护。
</li>
</ul>
   </td>
  </tr>
</table>


表 5. 安全性和法规遵从性的责任分担


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>使用 Azure Red Hat OpenShift 时的客户责任 


### <a name="customer-data-and-applications"></a>客户数据和应用程序

客户负责部署到 Azure Red Hat OpenShift 的应用程序、工作负载和数据。 但是，Microsoft 和 Red Hat 会提供各种工具来帮助客户管理平台上的数据和应用程序。


<table>
  <tr>
   <td><strong>资源</strong>
   </td>
   <td><strong>Microsoft 和 Red Hat 的帮助方式</strong>
   </td>
   <td><strong>客户责任</strong>
   </td>
  </tr>
  <tr>
   <td>客户数据 </td>
   <td>
<ul>

<li>维护由业界安全性和合规性标准定义的数据加密的平台级标准。 

<li>提供可帮助管理应用程序数据（如机密）的 OpenShift 组件。

<li>启用与第三方数据服务（例如 Azure SQL）的集成，用于在群集和/或 Microsoft 和 Red Hat Azure 之外存储和管理数据。
</li>
</ul>
   </td>
   <td>
<ul>

<li>维护与存储在平台上的所有客户数据以及客户应用程序使用和公开此数据的方式相关的责任。

<li>Etcd 加密
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>客户应用程序
   </td>
   <td>
<ul>

<li>使用安装的 OpenShift 组件预配群集，让客户可以访问 OpenShift 和 Kubernetes API 来部署和管理容器化应用程序。

<li>提供对 OpenShift API 的访问权限，让客户可以使用这些 API 设置操作员，将社区、第三方、Microsoft 和 Red Hat 以及 Red Hat 服务添加到群集。 

<li>提供存储类和插件以支持用于客户应用程序的永久性卷。
</li>
</ul>
   </td>
   <td>
<ul>

<li>维护客户和第三方应用程序、数据以及它们的完整生命周期。

<li>如果客户使用操作员或外部映像向群集中添加 Red Hat、社区、第三方、自己的服务或其他服务，则客户将对这些服务负责，并负责使用相应的提供程序（包括 Red Hat）来解决出现的任何问题。

<li>使用提供的工具和功能进行<a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">配置和部署</a>；<a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">保持最新版本</a>；<a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">设置资源请求和限制</a>；<a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">调整群集的大小，使其具有足够的资源来运行应用</a>；<a href="https://docs.openshift.com/aro/4/administering_a_cluster/">设置权限</a>；与其他服务集成；<a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">管理客户部署的任何映像流或模板</a>；<a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">外部服务</a>；保存、备份和还原数据；另外，还管理其高度可用且可复原的工作负载。

<li>维护对 Azure Red Hat OpenShift 上运行的应用程序的监视责任；包括安装和操作软件以收集指标和创建警报。
</li>
</ul>
   </td>
  </tr>
</table>


表 7. 客户数据、客户应用程序和服务的客户责任
