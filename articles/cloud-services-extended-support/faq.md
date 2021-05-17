---
title: Azure 云服务（外延支持）的常见问题解答
description: Azure 云服务（外延支持）的常见问题解答
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8707578eef3467cc24cfeb3cd1c5156d7f5b3b1d
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108288753"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure 云服务（外延支持）的常见问题解答
本文介绍与 Azure 云服务相关的常见问题（外延支持）。

## <a name="general"></a>常规

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>云服务（经典）和云服务（外延支持）的资源名称是什么？
- 云服务（经典）：`microsoft.classiccompute/domainnames`
- 云服务（外延支持）：`microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>哪些位置可用于部署云服务（外延支持）？
可以在所有公有云区域中采用云服务（外延支持）。

### <a name="how-does-my-quota-change"></a>我的配额将有什么变化？ 
客户需要使用与任何其他 Azure 资源管理器产品相同的过程来请求配额。Azure 资源管理器中的配额是区域性的，每个区域需要单独的配额请求。

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>为什么看不到生产和暂存槽？
云服务（外延支持）不支持托管服务的逻辑概念，而托管服务就包括上述两个槽，即生产和暂存。 每个部署都是独立的云服务（外延支持）部署。 若要测试云服务的新版本并对其进行暂存，请部署云服务部署（外延支持）并将其标记为可与其他云服务（外延支持）交换的 VIP

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>为什么我不能再创建一个空的云服务？
已不存在托管服务名称的概念，因此无法再创建创建空的云服务（外延支持）。

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>云服务（外延支持）是否支持资源运行状况检查 (RHC)？
不支持，云服务（外延支持）不支持资源运行状况检查 (RHC)。

### <a name="how-are-role-instance-metrics-changing"></a>角色实例指标有什么变化？
角色实例指标方面没有变化。 

### <a name="how-are-web--worker-roles-changing"></a>Web 角色和辅助角色有什么变化？
Web 角色和辅助角色的设计、体系结构或组件不会有任何变化。 

### <a name="how-are-role-instances-changing"></a>角色实例有什么变化？
角色实例的设计、体系结构或组件没有任何变化。 

### <a name="how-will-guest-os-updates-change"></a>来宾操作系统更新有什么变化？
 推出方法没有任何变化。 云服务（经典）和云服务（外延支持）会获得相同的更新。
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>云服务（外延支持）是否支持“已停止-已分配”和“已停止-已解除分配”状态？

云服务（外延支持）部署仅支持“已停止-分配”状态，该状态在 Azure 门户中显示为“已停止”。 不支持“已停止-已解除分配”状态。 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>云服务（外延支持）部署是否支持跨群集、可用性区域和区域进行缩放？
云服务（外延支持）部署不能跨多个群集、可用性区域和区域进行扩展。 

### <a name="how-can-i-get-the-deployment-id-for-my-cloud-service-extended-support"></a>如何获取云服务（外延支持）的部署 ID
可使用 [CloudServiceInstanceView](/rest/api/compute/cloudservices/getinstanceview#cloudserviceinstanceview) API 访问部署 ID（也称为专用 ID）。 也可从 Azure 门户上云服务（外延支持）的“角色和实例”边栏选项卡中获取

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>云服务（经典）与云服务（外延支持）的定价方面是否有任何差异？
云服务（外延支持）使用 Azure 密钥保管库和基本 (ARM) 公共 IP 地址。需要证书的客户需要使用 Azure Key Vault 进行证书管理（[详细了解](https://azure.microsoft.com/pricing/details/key-vault/) Azure Key Vault 定价。）云服务（外延支持）的每个公共 IP 地址均需单独付费（[详细了解](https://azure.microsoft.com/pricing/details/ip-addresses/)公共 IP 地址定价。） 
## <a name="resources"></a>资源 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>链接到云服务（外延支持）部署的哪些资源需要位于同一资源组中？
负载均衡器、网络安全组和路由表需要位于同一区域和资源组中。 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>链接到云服务（外延支持）部署的哪些资源需要位于同一区域中？
密钥保险库、虚拟网络、公共 IP 地址、网络安全组和路由表都需要位于同一区域中。

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>链接到云服务（外延支持）部署的哪些资源需要位于同一虚拟网络中？
公共 IP 地址、负载均衡器、网络安全组和路由表需要位于在同一个虚拟网络中。 

## <a name="deployment-files"></a>部署文件 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>如何使用模板来完成部署或管理部署？
可以使用 REST、PowerShell 和 CLI，将模板和参数文件作为参数进行传递。 也可以使用 Azure 门户来上传模板和参数文件。  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>现在是否需要维护四个文件？ （即 template、parameter、csdef 和 cscfg）
模板和参数文件仅用于实现部署自动化。 与云服务一样（经典），你可以先手动创建从属资源，然后再使用 PowerShell、CLI 命令完成云服务（外延支持）部署，也可以在门户中通过 csdef 和 cscfg 来完成。

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>我在云服务（外延支持）上的应用程序代码有什么变化
不需要对 .cspkg 中打包的应用程序代码进行任何更改。 现有的应用程序将继续像以前一样工作。 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>云服务（外延支持）是否允许 CTP 包格式？
云服务（外延支持）不支持 CTP 包格式。 但是，它允许提高的包大小限制 800 MB

## <a name="migration"></a>迁移

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>云服务（外延支持）是否会因为分配失败而降低故障？
不能，云服务（外延支持）部署与群集（如云服务（经典））有关。 因此，当群集已满时，分配失败的情况将继续存在。 

### <a name="when-do-i-need-to-migrate"></a>在什么情况下需要进行迁移？ 
估计所需的时间和复杂性迁移取决于一系列变量。 规划是了解迁移的工作范围、阻止程序和复杂性的最有效步骤。

## <a name="networking"></a>网络 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>为什么在没有虚拟网络的情况下无法创建部署？
虚拟网络是在 Azure 资源管理器上进行任何部署必不可少的资源。 云服务（外延支持）部署必须位于虚拟网络内。 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>为什么我现在看到如此多的网络资源？ 
在 Azure 资源管理器中，云服务（外延支持）部署的组件作为资源公开，以便更好地查看和改进控制。 云服务（经典）中使用了相同类型的资源，只是这些资源被隐藏了。 此类资源的一个示例是公共负载均衡器，它现在是平台自动创建的显式“只读”资源

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>子网在云服务（外延支持）方面有哪些限制？
包含云服务（外延支持）部署的子网不能与其他计算产品（例如虚拟机、虚拟机规模集、Service Fabric 等）中的部署共享。

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>云服务（外延支持）支持哪些 IP 分配方法？
云服务（外延支持）支持动态和静态 IP 分配方法。 静态 IP 地址在 cscfg 文件中作为保留 IP 被引用。

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>为什么我需要为 IP 地址付费？
客户需要为云服务（外延支持）上使用的 IP 地址付费，就如同用户需要为虚拟机相关的 IP 地址付费一样。 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>是否可以将 DNS 名称用于云服务（外延支持）？ 
是。 还可以为云服务（外延支持）提供 DNS 名称。 使用 Azure 资源管理器，DNS 标签是分配给云服务的公共 IP 地址的可选属性。 基于 Azure 资源管理器的部署的 DNS 名称格式为 `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>是否可以更新或更改现有云服务（外延支持）的虚拟网络引用？ 
不是。 在创建云服务的过程中，虚拟网络引用是必需的。 对于现有的云服务，无法更改虚拟网络引用。 可使用 VNet API 修改虚拟网络地址空间本身。 

## <a name="certificates--key-vault"></a>证书和密钥保险库

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>为什么需要在云服务（外延支持）上管理我的证书？
云服务（外延支持）采用了与其他计算服务相同的过程，其中证书驻留在客户托管的密钥保管库中。 这使客户可以完全控制其机密和证书。 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>是否可以对所有区域中的所有部署使用一个密钥保险库？
不是。 密钥保险库是一种区域资源，客户需要在每个区域中提供一个密钥保险库。 但是，可以将一个密钥保险库用于给定区域中的所有部署。

## <a name="next-steps"></a>后续步骤
要开始使用云服务（外延支持），请参阅[使用 PowerShell 部署云服务（外延支持）](deploy-powershell.md)