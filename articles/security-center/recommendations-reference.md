---
title: 所有 Azure 安全中心建议的参考表
description: 本文列出了 Azure 安全中心的安全建议，这些建议可帮助强化和保护你的资源。
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/26/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: f6d881036b0ebaa79d1052e41b8ec649bb1e8d6d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797155"
---
# <a name="security-recommendations---a-reference-guide"></a>安全建议 - 参考指南

本文列出了 Azure 安全中心可能会显示的建议。 环境中显示的建议取决于要保护的资源和自定义的配置。

安全中心的建议以 [Azure 安全基准](../security/benchmarks/introduction.md)为基础。 Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。

若要了解如何响应这些建议，请参阅 [Azure 安全中心的修正建议](security-center-remediate-recommendations.md)。

安全分数基于已完成的安全中心建议的数量。 若要确定首先要解决的建议，请查看每个建议的严重级别及其对安全分数的潜在影响。

> [!TIP]
> 如果建议的描述中显示“无相关策略”，通常是因为该建议依赖于另一个建议及其策略。 例如，建议“应修正 Endpoint Protection 运行状况失败...”依赖于建议“应安装 Endpoint Protection 解决方案...”，后者检查 Endpoint Protection 解决方案是否已安装。 基础建议 _确实_ 具有一个策略。
> 将策略限制为仅限基础建议可简化策略管理。

## <a name="compute-recommendations"></a><a name='recs-compute'></a>计算建议

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>数据建议

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess 建议

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>网络建议

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>弃用的建议

|建议|说明及相关策略|严重性|已启用快速修复？（[了解详细信息](security-center-remediate-recommendations.md#quick-fix-remediation)）|资源类型|
|----|----|----|----|----|
|应限制对应用服务的访问|通过更改网络配置来限制对应用服务的访问，以拒绝来自过大范围的入站流量。<br>（相关策略：[预览]：应限制对应用服务的访问）|高|N|应用服务|
|应强化 IaaS NSG 上 Web 应用的规则|如果运行 web 应用程序的虚拟机的网络安全组 (NSG) 所包含的 NSG 规则对于 web 应用程序端口而言过于宽松，应强化这些安全组。<br>（相关策略：应该强化 IaaS 上 Web 应用程序的 NSG 规则）|高|N|虚拟机|
|应定义 Pod 安全策略，通过删除不必要的应用程序特权来减少攻击途径。|通过删除不必要的应用程序特权，来定义 Pod 安全策略以减少攻击途径。 建议配置 pod 安全策略，以便 pod 只能访问其有权访问的资源。<br>（相关策略：[预览]：应在 Kubernetes 服务上定义 Pod 安全策略）|中型|N|计算资源（容器）|
|安装适用于 IoT 的 Azure 安全中心安全模块，以更深入地了解 IoT 设备|安装适用于 IoT 的 Azure 安全中心安全模块，以更深入地了解 IoT 设备。|低|N|IoT 设备|

## <a name="next-steps"></a>后续步骤

若要详细了解建议，请参阅以下内容：

- [Azure 安全中心的安全建议](security-center-recommendations.md)
- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
