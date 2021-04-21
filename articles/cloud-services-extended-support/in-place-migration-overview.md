---
title: 将 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）
description: 有关从云服务（经典）迁移到云服务（外延支持）的概述
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286687"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>将 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）

本文概述了平台支持的迁移工具，以及如何使用此工具将 [Azure 云服务（经典）](../cloud-services/cloud-services-choose-me.md)迁移到 [Azure 云服务（外延支持）](overview.md)。

迁移工具利用相同的 API，其体验与[虚拟机（经典）迁移](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview)相同。 

> [!IMPORTANT]
> 使用迁移工具从云服务（经典）迁移到云服务（外延支持）目前为公开预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在迁移过程中如需帮助，请参阅以下资源： 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html)：Microsoft 和社区对迁移的支持。
- [Azure 迁移支持](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D)：迁移过程中的技术协助专门支持团队。 没有技术支持的客户可以使用专门为此迁移提供的[免费支持功能](https://aka.ms/cs-migration-errors)。
- 如果你的公司/组织已与 Microsoft 合作，或者与 Microsoft 代表（例如云解决方案架构师或技术客户经理）合作，请联系他们以获取更多的迁移资源。
- 请完成[此调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u)，以便向云服务（外延支持）产品团队提供反馈或提出问题。 

## <a name="migration-benefits"></a>迁移优点
平台支持的迁移提供以下重要优势：
- 迁移完全由平台协调，可将整个部署和关联的资源移到 Azure 资源管理器。
- 迁移过程不会造成停机。
- 最大程度地减少手动任务，相比其他迁移路径更简单且更快速。 
- 在迁移过程中保留云服务 IP 地址和 DNS 标签。 

有关其他优势以及迁移的原因，请参阅[云服务（外延支持）](overview.md)和 [Azure 资源管理器](../azure-resource-manager/management/overview.md)。 

## <a name="setup-access-for-migration"></a>设置迁移访问权限

若要执行此迁移，必须将你添加为订阅的共同管理员，并注册所需的提供程序。 

1. 登录到 Azure 门户。
3. 在“中心”菜单上中选择“订阅”。  如果看不到该选项，请选择“所有服务”。
3. 查找相应订阅项，然后查看“我的角色”字段。 对于共同管理员，该值应是“帐户管理员”。如果你无法添加共同管理员，请联系订阅的服务管理员或共同管理员，将自己添加为共同管理员。

4. 使用[门户](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) 注册 Microsoft.ClassicInfrastructureMigrate 命名空间的订阅

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. 使用[门户](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) 注册云服务迁移预览功能的订阅

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. 检查注册状态。 完成注册可能需要几分钟时间。 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>云服务（经典）迁移与虚拟机（经典）迁移有何差别？
Azure Service Manager 支持两种不同的计算产品：[Azure 虚拟机（经典）](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic)和 [Azure 云服务（经典）](../cloud-services/cloud-services-choose-me.md)或 Web/辅助角色。 这两种产品根据云服务中的部署类型而有差异。 Azure 云服务（经典）使用包含具有 Web/辅助角色的部署的云服务。 Azure 虚拟机（经典）使用包含具有 IaaS VM 的部署的云服务。

由于部署类型的差异，云服务（经典）和虚拟机（经典）的支持方案列表也有差异。

## <a name="migration-steps"></a>迁移步骤
 
客户可以使用用于迁移虚拟机（经典）的相同四个操作来迁移其云服务（经典）部署。 

1. 验证迁移 - 验证迁移是否未被常见的不受支持方案阻止。
2. 准备迁移 – 在 Azure 资源管理器中复制资源元数据。 锁定用于创建/更新/删除操作的所有资源，以确保资源元数据在 Azure 服务器管理器和 Azure 资源管理器中同步。 所有读取操作将使用云服务（经典）和云服务（外延支持）API 运行。
3. 中止迁移 - 从 Azure 资源管理器中删除资源元数据。 解锁用于创建/更新/删除操作的所有资源。
4. 提交迁移 - 从 Azure Service Manager 中删除资源元数据。 解锁用于创建/更新/删除操作的资源。 尝试提交后，不再允许中止。

>[!NOTE]
> 准备、中止和提交操作是幂等的，因此如果失败，重试操作应可解决问题。

:::image type="content" source="media/in-place-migration-1.png" alt-text="插图显示了与迁移关联的步骤示意图。":::

有关详细信息，请参阅[平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>可用于与云服务（经典）关联的迁移的受支持资源和功能
-   存储帐户
-   虚拟网络
-   网络安全组
-   保留的公共 IP 地址
-   终结点访问控制列表
-   用户定义路由
-   内部负载均衡器
-   将证书迁移到密钥保管库
-   插件和扩展（基于 XML 和 JSON）
-   启动时/停止时任务
-   具有加速网络的部署
-   使用一个或多个角色的部署
-   基本负载均衡器
-   输入、实例输入、内部终结点
-   动态公共 IP 地址
-   DNS 名称 
-   网络流量规则
-   Hypernet 虚拟网络

## <a name="supported-configurations--migration-scenarios"></a>支持的配置/迁移方案
这是一些涉及到资源、功能和云服务组合的主要方案。 此列表并未囊括所有方式。

| 服务 | 配置 | 注释 | 
|---|---|---|
| [Azure AD 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | 包含 Azure Active Directory 域服务的虚拟网络。 | 支持包含云服务部署和 Azure AD 域服务的虚拟网络。 客户首先需要单独迁移 Azure AD 域服务，然后迁移剩下的仅具有云服务部署的虚拟网络 |
| 云服务 | 仅限在单个槽中具有部署的云服务。 | 可以迁移包含生产槽部署或过渡槽部署的云服务 |
| 云服务 | 不在公开可见的虚拟网络中的部署（默认虚拟网络部署） | 云服务可以位于公开可见的虚拟网络或隐藏的虚拟网络中，也可以不在任何虚拟网络中。  支持迁移位于隐藏虚拟网络和公开可见虚拟网络中的云服务。 客户可以使用“验证”API 来判断部署是否位于默认虚拟网络中，因而可确定该部署是否可迁移。 |
|云服务 | XML 扩展（BGInfo、Visual Studio 调试器、Web 部署和远程调试）。 | 支持迁移所有 XML 扩展 
| 虚拟网络 | 包含多个云服务的虚拟网络。 | 支持迁移包含多个云服务的虚拟网络。 虚拟网络及其包含的所有云服务将一起迁移到 Azure 资源管理器。 |
| 虚拟网络 | 通过门户迁移创建的虚拟网络（需要在 .cscfg 文件中使用“Group Resource-group-name VNet-Name”）  | 在迁移过程中，cscfg 中的虚拟网络名称将更改为使用虚拟网络的 Azure 资源管理器 ID。 (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>若要在迁移后管理部署，请更新 .cscfg 文件的本地副本，以开始使用 Azure 资源管理器 ID 而不是虚拟网络名称。 <br><br>使用旧命名方案的 .cscfg 文件将通不过验证。 
| 虚拟网络 | 迁移在不同子网中具有角色的部署。 | 支持迁移在不同子网中具有不同角色的云服务。 |
    

## <a name="resources-and-features-not-available-for-migration"></a>不可迁移的资源和功能
这是一些涉及到资源、功能和云服务组合的主要方案。 此列表并未囊括所有方式。 

| 资源 | 后续步骤/解决方法 | 
|---|---|
| 自动缩放规则 | 迁移会继续，但规则将被删除。 迁移后在云服务（外延支持）上[重新创建规则](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling)。 | 
| 警报 | 迁移会继续，但警报将被删除。 迁移后在云服务（外延支持）上[重新创建规则](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts)。 | 
| VPN 网关 | 开始迁移之前请删除 VPN 网关，并在迁移完成后重新创建 VPN 网关。 | 
| Express Route 网关（仅限在虚拟网络所在的同一订阅中） | 开始迁移之前请删除 Express Route 网关，并在迁移完成后重新创建该网关。 | 
| Quota  | 配额不会迁移。 迁移之前在 Azure 资源管理器上[请求新配额](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution)，以便验证成功。 | 
| 地缘组 | 不支持。 在迁移之前删除任何地缘组。  | 
| 使用[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)的虚拟网络| 在迁移某个已对等互连到另一虚拟网络的虚拟网络之前，请删除对等互连，将该虚拟网络迁移到资源管理器，然后重新创建对等互连。 这可能会导致停机，具体取决于体系结构。 | 
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
| 迁移包含远程桌面插件和远程桌面扩展的部署 | 选项 1：在迁移之前删除远程桌面插件。 这需要对部署文件进行更改。 然后，迁移将会继续。 <br><br> 选项 2：删除远程桌面扩展并迁移部署。 迁移后，删除插件并安装扩展。 这需要对部署文件进行更改。 <br><br> 在迁移之前删除插件和扩展。 [不建议将插件](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates)用于云服务（外延支持）。| 
| 具有 PaaS 和 IaaS 部署的虚拟网络 |不支持 <br><br> 将 PaaS 或 IaaS 部署移到不同的虚拟网络中。 这会造成停机。 | 
使用旧式角色大小（例如“小”或“超大”）的云服务部署。 | 迁移将会完成，但角色大小将更新为使用新式角色大小。 成本或 SKU 属性不会有变化，并且进行此项更改后虚拟机不会重新启动。 更新所有部署项目以引用这些新式角色大小。 有关详细信息，请参阅[可用的 VM 大小](available-sizes.md)。|
| 将云服务迁移到不同的虚拟网络 | 不支持 <br><br> 1. 迁移之前将部署移到不同的经典虚拟网络。 这会造成停机。 <br> 2. 将新虚拟网络迁移到 Azure 资源管理器。 <br><br> 或 <br><br> 1. 将虚拟网络迁移到 Azure 资源管理器 <br>2. 将云服务移到新虚拟网络。 这会造成停机。 | 
| 位于虚拟网络中但未分配有显式子网的云服务 | 不支持。 缓解措施涉及到将角色移入子网中，这需要重启角色（造成停机） | 


## <a name="post-migration-changes"></a>迁移后的更改
云服务（经典）部署将转换为云服务（外延支持）部署。 有关更多详细信息，请参阅[云服务（外延支持）文档](deploy-prerequisite.md)。  

### <a name="changes-to-deployment-files"></a>对部署文件的更改 

将对客户的 .csdef 和 .cscfg 文件进行次要更改，使部署文件符合 Azure 资源管理器和云服务（外延支持）要求。 迁移后，检索新的部署文件或更新现有文件。 对于更新/删除操作需要采取此措施。  

- 虚拟网络 .cscfg 文件的 NetworkConfiguration 节中使用完整的 Azure 资源管理器资源 ID，而不只是使用资源名称。 例如，`/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`。 对于属于云服务所在的同一资源组的虚拟网络，可以选择将 .cscfg 文件重新更新为仅使用虚拟网络名称。  

- 经典大小（例如“小”、“大”、“超大”）将替换为其新的大小名称 Standard_A*。 需要在 .csdef 文件中将这些大小名称更改为其新名称。 有关详细信息，请参阅[云服务（外延支持）部署先决条件](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- 使用“获取”API 获取部署文件的最新副本。 
    - 使用[门户](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates)、[CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates) 和 [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 获取模板 
    - 使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) 或 [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package) 获取 .csdef 文件。 
    - 使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) 或 [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package) 获取 .cscfg 文件。 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>对客户的自动化、CI/CD 管道、自定义脚本、自定义仪表板、自定义工具等的更改  

客户需要更新其工具和自动化，以开始使用新的 API/命令来管理其部署。 在进行此项更改的过程中，客户可以轻松采用 Azure 资源管理器/云服务（外延支持）的新特性和功能。 

- 迁移后对资源和资源组名称的更改
    - 在迁移过程中，少量资源（例如云服务、公共 IP 地址等）的名称会更改。 在更新云服务之前，可能需要在部署文件中反映这些更改。 [详细了解资源名称更改](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration)。  

- 重新创建管理和缩放云服务所需的规则与策略 
    - [自动缩放规则](configure-scaling.md)不会迁移。 迁移后，请重新创建自动缩放规则。  
    - [警报](enable-alerts.md)不会迁移。 迁移后，请重新创建警报。
    - 将创建没有任何访问策略的密钥保管库。 在密钥保管库上[创建适当的策略](../key-vault/general/assign-access-policy-portal.md)以查看或管理证书。 证书将显示在名为“机密”的选项卡上的“设置”下。

## <a name="next-steps"></a>后续步骤
- [平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述](../virtual-machines/migration-classic-resource-manager-overview.md)
- 使用 [Azure 门户](in-place-migration-portal.md)迁移到云服务（外延支持）
- 使用 [PowerShell](in-place-migration-powershell.md) 迁移到云服务（外延支持）
