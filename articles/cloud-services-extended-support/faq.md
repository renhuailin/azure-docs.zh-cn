---
title: 'Azure 云服务的常见问题 (扩展支持) '
description: 'Azure 云服务的常见问题 (扩展支持) '
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744248"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure 云服务（外延支持）的常见问题解答
本文介绍与 Azure 云服务相关的常见问题 (扩展支持) 。

## <a name="general"></a>常规

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>云服务 (经典) & 云服务 (扩展支持) 的资源名称是多少？
- 云服务 (经典) ： `microsoft.classiccompute/domainnames`
- 云服务 (扩展支持) ： `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>哪些位置可用于部署云服务 (扩展支持) ？
云服务 (扩展支持) 在所有公有云区域中均可用。

### <a name="how-does-my-quota-change"></a>如何更改我的配额？ 
客户需要使用与任何其他 Azure 资源管理器产品相同的过程请求配额。Azure 资源管理器中的配额为地区性，每个区域需要单独的配额请求。

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>为什么不会再看到生产 & 过渡槽？
云服务 (扩展支持) 不支持托管服务的逻辑概念，其中包括两个槽 (生产 & 过渡) 。 每个部署都是独立的云服务 (扩展支持) 部署。 若要测试云服务的新版本并对其进行暂存，请将云服务部署 (扩展支持) 并将其标记为可与其他云服务 (扩展支持的 VIP 交换) 

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>为什么我不能再创建一个空的云服务？
托管服务名称的概念不再存在，因此不能 (扩展支持) 创建一个空的云服务。

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>云服务是否 (扩展支持) 支持资源运行状况检查 (RHC) ？
不能，云服务 (扩展支持) 不支持资源运行状况检查 (RHC) 。

### <a name="how-are-role-instance-metrics-changing"></a>角色实例指标如何变化？
角色实例指标中没有更改。 

### <a name="how-are-web--worker-roles-changing"></a>Web & 辅助角色如何变化？
Web 角色和辅助角色的设计、体系结构或组件不会有任何变化。 

### <a name="how-are-role-instances-changing"></a>角色实例如何变化？
角色实例的设计、体系结构或组件不会有任何变化。 

### <a name="how-will-guest-os-updates-change"></a>如何更改来宾 os 更新？
 不会更改推出方法。 云服务 (经典) 和云服务 (扩展支持) 会获得相同的更新。
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>云服务 (扩展支持是否) 支持已停止-已分配和已停止-已解除分配的状态？

) 部署的云服务 (扩展支持仅支持在 Azure 门户中显示为 "已停止" 的停止分配的状态。 已停止-解除分配状态不受支持。 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>云服务 (扩展支持) 部署是否支持跨群集、可用性区域和区域进行缩放？
云服务 (扩展支持) 部署不能跨多个群集、可用性区域和区域进行扩展。 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>云服务 (经典) 与云服务之间是否存在任何定价差异 (扩展支持) ？
云服务 (扩展支持) 使用 Azure Key Vault 和基本 (ARM) 公共 IP 地址。需要证书的客户需要使用 Azure Key Vault 进行证书管理 ([了解](https://azure.microsoft.com/pricing/details/key-vault/) 有关 Azure Key Vault 定价的详细信息。 ) 云服务的每个公共 IP 地址 (向单独收费) 扩展支持 ([详细了解](https://azure.microsoft.com/pricing/details/ip-addresses/) 公共 IP 地址定价。 )  
## <a name="resources"></a>资源 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>哪些资源链接到云服务 (扩展的支持) 部署需要在同一资源组中？
负载均衡器、网络安全组和路由表需要位于同一区域和资源组中。 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>哪些资源链接到云服务 (扩展支持) 部署需要在同一区域中运行？
Key Vault、虚拟网络、公共 IP 地址、网络安全组和路由表都需要在同一区域中。

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>哪些资源链接到云服务 (扩展支持) 部署需要驻留在同一个虚拟网络中？
公共 IP 地址、负载均衡器、网络安全组和路由表需要驻留在同一个虚拟网络中。 

## <a name="deployment-files"></a>部署文件 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>如何使用模板来部署或管理部署？
可以使用 REST、PowerShell 和 CLI 将模板和参数文件作为参数进行传递。 还可以使用 Azure 门户上传它们。  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>现在是否需要维护四个文件？  (template、parameter) 、
模板和参数文件仅用于部署自动化。 与云服务一样 (经典) ，你可以先手动创建从属资源，然后再使用 PowerShell、CLI 命令或通过现有的标准支持的门户通过门户 (扩展支持) 部署。

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>如何在云服务上更改应用程序代码 (扩展支持) 
不需要对 .cspkg 中打包的应用程序代码进行任何更改。 你的现有应用程序将继续像以前一样工作。 


## <a name="migration"></a>迁移

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>云服务 (扩展支持) 是否会因为分配失败而降低故障？
不能，云服务 (扩展支持) 部署与群集（如云服务 (经典) ）相关联。 因此，当群集已满时，分配失败将继续存在。 

### <a name="when-do-i-need-to-migrate"></a>何时需要迁移？ 
估计所需的时间和复杂性迁移取决于一系列变量。 规划是了解工作范围、阻止程序和迁移复杂性的最有效步骤。

## <a name="networking"></a>网络

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>为什么无法在没有虚拟网络的情况下创建部署？
虚拟网络是 Azure 资源管理器上的任何部署所需的资源。 云服务 (扩展支持) 部署必须位于虚拟网络内。 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>为什么我现在看到如此多的网络资源？ 
在 Azure 资源管理器中，云服务的组件 (扩展支持) 部署作为资源公开，以便更好地查看和改进控制。  (经典) 的云服务中使用了相同类型的资源，但这些资源只是隐藏的。 此类资源的一个示例是公共负载均衡器，它现在是平台自动创建的显式 "只读" 资源

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>与云服务相关的子网有哪些限制 (扩展支持) ？
包含云服务 (扩展支持) 部署的子网不能与其他计算产品（例如虚拟机、虚拟机规模集、Service Fabric 等）中的部署共享。

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>云服务 (扩展支持) 支持哪些 IP 分配方法？
支持动态 & 静态 IP 分配方法) 云服务 (扩展支持。 静态 IP 地址作为 .cscfg 文件中的保留 Ip 被引用。

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>为什么要向 IP 地址收费？
客户需要对云服务)  (的 IP 地址使用进行计费，就像对与虚拟机关联的 IP 地址计费一样。 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>是否可以将 DNS 名称用于云服务 (扩展支持) ？ 
是。 还可以为云服务 (扩展支持) 提供 DNS 名称。 使用 Azure 资源管理器，DNS 标签是分配给云服务的公共 IP 地址的可选属性。 基于 Azure 资源管理器的部署的 DNS 名称的格式为 `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>证书 & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>为什么需要在云服务上管理我的证书 (扩展支持) ？
云服务 (扩展支持) 采用了与其他计算服务相同的过程，其中证书驻留在客户托管的密钥保管库中。 这样，客户便可以完全控制其机密 & 证书。 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>是否可以对所有区域中的所有部署使用一个 Key Vault？
不能。 Key Vault 是一种区域资源，客户需要在每个区域中有一个 Key Vault。 但是，可以将一个 Key Vault 用于给定区域中的所有部署。

## <a name="next-steps"></a>后续步骤
若要开始使用云服务 (扩展支持) ，请参阅 [使用 PowerShell 部署云服务 (扩展支持) ](deploy-powershell.md)