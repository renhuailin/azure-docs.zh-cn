---
title: 迁移到 Azure 云服务（外延支持）的技术详细信息和要求
description: 提供从 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）的技术详细信息和要求
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.openlocfilehash: 55ce5305962562876a97dfd7677e6af5e1eb9e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747565"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>迁移到 Azure 云服务（外延支持）的技术详细信息   

本文讨论与云服务（经典）相关的迁移工具相关技术详细信息。 

## <a name="details-about-feature--scenarios-supported-for-migration"></a>迁移所支持的功能/方案的相关详细信息 


### <a name="extensions-and-plugin-migration"></a>扩展和插件迁移 
- 所有已启用并受支持的扩展都会进行迁移， 
- 已禁用的扩展则不会进行迁移。 
- 插件是一种旧概念，应该在迁移前予以删除。 支持迁移插件，但在迁移之后，如果需要启用扩展，则必须先删除插件，然后才能安装扩展。 远程桌面插件和扩展受此影响最大。   
 
### <a name="certificate-migration"></a>证书迁移
- 在云服务（外延支持）中，证书存储在密钥保管库中。 在迁移过程中，我们会为客户创建具有云服务名称的密钥保管库，并将所有证书从 Azure Service Manager 传输到密钥保管库。 
- 对该密钥保管库的引用是在模板中指定，或通过 PowerShell 或 Azure CLI 传递。 

### <a name="service-configuration-and-service-definition-files"></a>服务配置和服务定义文件
- 需要为云服务（外延支持）更新 .cscfg 和 .csdef 文件，进行少量更改。 
- 虚拟网络和 VM SKU 等资源的名称不相同。 请参阅[迁移后的资源转换和命名约定](#translation-of-resources-and-naming-convention-post-migration)
- 客户可以通过 [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 和 [Rest API](/rest/api/compute/cloudservices/get) 检索其新部署。 

### <a name="cloud-service-and-deployments"></a>云服务和部署
- 每个云服务（外延支持）部署都是独立的云服务。 部署不再使用槽分组到云服务中。
- 如果云服务（经典）中有两个槽，你需要删除一个槽（过渡），并使用迁移工具将另一个（生产）槽移至 Azure 资源管理器。 
- 迁移至 Azure 资源管理器之后，云服务部署上的公共 IP 地址保持不变，并以基本 SKU IP（动态或静态）资源的形式公开。 
- 已迁移的云服务的 DNS 名称和域 (cloudapp.azure.net) 保持不变。 

### <a name="virtual-network-migration"></a>虚拟网络迁移
- 如果云服务部署位于虚拟网络中，则在迁移过程中，所有的云服务和关联的虚拟网络资源会一起迁移。 
- 迁移后，该虚拟网络与该云服务位于不同的资源组中。 
- 对于包含多个云服务的虚拟网络，各个云服务会一个接一个地迁移。 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>不在虚拟网络中的部署的迁移
- 在 2017 年，Azure 开始将新部署（不含由客户指定的虚拟网络）自动创建到平台所创建的“默认”虚拟网络中。 这些默认虚拟网络对客户不可见。   
- 在迁移过程中，这个默认虚拟网络会在 Azure 资源管理器中向客户公开一次。 若要在 Azure 资源管理器中管理或更新该部署，客户需要将该虚拟网络信息添加至 .cscfg 文件的 NetworkConfiguration 部分。    
- 迁移至 Azure 资源管理器后，默认虚拟网络与云服务位于同一个资源组中。
- 在此时间之前创建的云服务不会位于任何虚拟网络中，因此无法使用该工具进行迁移。 请考虑直接在 Azure 资源管理器中重新部署这些云服务。 
- 若要检查某个部署是否符合迁移条件，请在该部署上运行验证 API。 验证 API 的结果会包含错误消息，明确指出该部署是否符合迁移条件。     

### <a name="load-balancer"></a>负载均衡器   
- 对于使用公用终结点的云服务，在 Azure 资源管理器中的客户订阅中，将会公开一个由平台创建并与该云服务相关联的负载均衡器。 负载均衡器是只读的资源，只能通过服务配置 (.cscfg) 和服务定义 (.csdef) 文件进行更新。 

### <a name="key-vault"></a>密钥保管库
- 在迁移过程中，Azure 会自动创建新的密钥保管库，并将所有证书迁移到其中。 该工具不允许使用现有的密钥保管库。 
- 云服务（外延支持）需要一个位于同一区域和订阅中的密钥保管库， 该密钥保管库会在迁移过程中自动创建。 

## <a name="resources-and-features-not-available-for-migration"></a>不可迁移的资源和功能
这是一些涉及到资源、功能和云服务组合的主要方案。 此列表并未囊括所有方式。 

| 资源 | 后续步骤/解决方法 | 
|---|---|
| 自动缩放规则 | 迁移会继续，但规则将被删除。 迁移后在云服务（外延支持）上[重新创建规则](./configure-scaling.md)。 | 
| 警报 | 迁移会继续，但警报将被删除。 迁移后在云服务（外延支持）上[重新创建规则](./enable-alerts.md)。 | 
| VPN 网关 | 开始迁移之前请删除 VPN 网关，并在迁移完成后重新创建 VPN 网关。 | 
| Express Route 网关（仅限在虚拟网络所在的同一订阅中） | 开始迁移之前请删除 Express Route 网关，并在迁移完成后重新创建该网关。 | 
| Quota  | 配额不会迁移。 迁移之前在 Azure 资源管理器上[请求新配额](../azure-resource-manager/templates/error-resource-quota.md#solution)，以便验证成功。 | 
| 地缘组 | 不支持。 在迁移之前删除任何地缘组。  | 
| 使用[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)的虚拟网络| 在迁移某个已对等互连到另一虚拟网络的虚拟网络之前，请删除对等互连，将该虚拟网络迁移到资源管理器，然后重新创建对等互连。 这可能会导致停机，具体取决于体系结构。 | 
| 包含应用服务环境的虚拟网络 | 不支持 | 
| 包含 HDInsight 服务的虚拟网络 | 不支持。 
| 包含 Azure API 管理部署的虚拟网络 | 不支持。 <br><br> 若要迁移虚拟网络，请更改 API 管理部署的虚拟网络。 此操作不会造成停机。 | 
| 经典 Express Route 线路 | 不支持。 <br><br>这些线路需要在开始 PaaS 迁移之前迁移到 Azure 资源管理器。 有关详细信息，请参阅[将 ExpressRoute 线路从经典部署模型转移到资源管理器部署模型](../expressroute/expressroute-howto-move-arm.md)。 |  
| 基于角色的访问控制 | 迁移后，资源的 URI 将从 `Microsoft.ClassicCompute` 更改为 `Microsoft.Compute`。迁移后需要更新 RBAC 策略。 | 
| 应用程序网关 | 。 <br><br> 开始迁移之前删除应用程序网关，并在完成到 Azure 资源管理器的迁移后重新创建应用程序网关 | 

## <a name="unsupported-configurations--migration-scenarios"></a>不支持的配置/迁移方案

| 配置/方案  | 后续步骤/解决方法 | 
|---|---|
| 迁移某些不在虚拟网络中的旧部署 | 不支持迁移某些不在虚拟网络中的云服务部署。 <br><br> 1. 使用“验证”API 检查部署是否符合迁移条件。 <br> 2. 如果符合条件，则部署将迁移到 Azure 资源管理器中前缀为“DefaultRdfeVnet”的虚拟网络下 | 
| 迁移使用动态 IP 地址的、包含生产槽部署和过渡槽部署的部署 | 迁移双槽云服务需要删除过渡槽。 删除过渡槽后，在 Azure 资源管理器中将生产槽作为独立的云服务（外延支持）迁移。 然后，将过渡环境重新部署为新的云服务（外延支持），并将其设置为可与第一个云服务交换。 | 
| 迁移使用保留 IP 地址的、包含生产槽部署和过渡槽部署的部署 | 不支持。 | 
| 迁移位于不同虚拟网络中的生产部署和过渡部署|迁移双槽云服务需要删除过渡槽。 删除过渡槽后，在 Azure 资源管理器中将生产槽作为独立的云服务（外延支持）迁移。 然后，可以在为新的云服务（外延支持）部署启用可交换属性后，将其链接到已迁移的部署。 可以重复使用旧过渡槽部署的部署文件来创建这个新的可交换部署。 | 
| 迁移空云服务（无部署的云服务） | 不支持。 | 
| 迁移包含远程桌面插件和远程桌面扩展的部署 | 选项 1：在迁移之前删除远程桌面插件。 这需要对部署文件进行更改。 然后，迁移将会继续。 <br><br> 选项 2：删除远程桌面扩展并迁移部署。 迁移后，删除插件并安装扩展。 这需要对部署文件进行更改。 <br><br> 在迁移之前删除插件和扩展。 [不建议将插件](./deploy-prerequisite.md#required-service-definition-file-csdef-updates)用于云服务（外延支持）。| 
| 具有 PaaS 和 IaaS 部署的虚拟网络 |不支持 <br><br> 将 PaaS 或 IaaS 部署移到不同的虚拟网络中。 这会造成停机。 | 
使用旧式角色大小（例如“小”或“超大”）的云服务部署。 | 迁移将会完成，但角色大小将更新为使用新式角色大小。 成本或 SKU 属性不会有变化，并且进行此项更改后虚拟机不会重新启动。 更新所有部署项目以引用这些新式角色大小。 有关详细信息，请参阅[可用的 VM 大小](available-sizes.md)。|
| 将云服务迁移到不同的虚拟网络 | 不支持 <br><br> 1. 迁移之前将部署移到不同的经典虚拟网络。 这会造成停机。 <br> 2. 将新虚拟网络迁移到 Azure 资源管理器。 <br><br> 或 <br><br> 1. 将虚拟网络迁移到 Azure 资源管理器 <br>2. 将云服务移到新虚拟网络。 这会造成停机。 | 
| 位于虚拟网络中但未分配有显式子网的云服务 | 不支持。 缓解措施涉及到将角色移入子网中，这需要重启角色（造成停机） | 

## <a name="translation-of-resources-and-naming-convention-post-migration"></a>迁移后的资源转换和命名约定
在迁移过程中，资源名称会更改，而且有些云服务功能会以 Azure 资源管理器资源的形式公开。 下表总结云服务迁移特定的更改。

| 云服务（经典） <br><br> 资源名称 | 云服务（经典） <br><br> 语法| 云服务（外延支持） <br><br> 资源名称| 云服务（外延支持） <br><br> 语法 | 
|---|---|---|---|
| 云服务 | `cloudservicename` | 未关联| 未关联 |
| 部署（由门户创建） <br><br> 部署（并非由门户创建）  | `deploymentname` | 云服务（外延支持） | `cloudservicename` |  
| 虚拟网络 | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> 未关联 |  虚拟网络（并非由门户创建） <br><br> 虚拟网络（由门户创建） <br><br> 虚拟网络（默认） | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `VNet-cloudservicename`|
| 未关联 | 未关联 | 密钥保管库 | `KV-cloudservicename` | 
| 未关联 | 未关联 | 云服务部署的资源组 | `cloudservicename-migrated` | 
| 未关联 | 未关联 | 虚拟网络的资源组 | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| 未关联 | 未关联 | 公共 IP（动态） | `cloudservicenameContractContract` | 
| 保留 IP 名称 | `reservedipname` | 保留 IP（并非由门户创建） <br><br> 保留 IP（由门户创建） | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| 未关联| 未关联 | 负载均衡器 | `LB-cloudservicename`|



## <a name="migration-issues-and-how-to-handle-them"></a>迁移问题及其处理方式。 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>迁移长时间停滞在某一项操作中。 
- 提交、准备和中止可能需要很长时间，具体取决于部署数量。 操作将在 24 小时后超时。   
- 提交、准备和中止操作都是幂等的。 大部分问题可以通过重试来修正。 可能存在暂时性错误，这些错误可能会在几分钟后消失，我们建议你稍候片刻，然后重试。 如果使用 Azure 门户进行迁移，但操作停滞在“正在进行”状态，请使用 PowerShell 重试该操作。 
- 请联系支持人员，从后端帮助你迁移或回滚该部署。 

### <a name="migration-failed-in-an-operation"></a>迁移在操作中失败。 
- 如果验证失败，则原因是该部署或虚拟网络包含不受支持的方案/功能/资源。 请使用不支持的方案列表在文档中查找解决方法。  
- 准备操作会先执行验证，包括未涵盖在“验证”中的一些高成本验证。 准备失败的原因可能是方案不受支持， 请在公用文档中查找该方案和解决方法。 需要调用“中止”以回到原始状态，并解除部署锁定以执行更新和删除操作。
- 如果中止失败，请重试该操作。 如果重试失败，请联系支持人员。
- 如果提交失败，请重试该操作。 如果重试失败，请联系支持人员。 即使在提交失败时，部署也不应有任何数据平面问题。 你的部署应能够处理客户流量，不会出现任何问题。 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>完成准备后，门户随即刷新。 体验会重新开始，“提交”或“中止”不再可见。 
- 门户会将迁移信息存储在本地，因此在刷新后，它会从“验证”阶段开始，即使云服务处于准备阶段也是如此。  
- 你可使用门户来完成“验证”和“准备”步骤，以公开“中止”和“提交”按钮。 这不会导致任何故障。
- 客户可以使用 PowerShell 或 Rest API 来中止或提交。 

### <a name="how-much-time-can-the-operations-takebr"></a>操作可能会花费多长时间？<br>
按照设计，执行验证的速度非常快。 运行准备的时间最长，需要一些时间，具体取决于所要迁移的角色实例总数。 中止和提交也需要一些时间，但与准备相比需要的时间较少。 所有操作都会在 24 小时后超时。

## <a name="next-steps"></a>后续步骤
如需将云服务（经典）部署迁移到云服务（扩展支持）方面的帮助，请参阅[支持和故障排除](support-help.md)登陆页面。