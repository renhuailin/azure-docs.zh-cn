---
title: 将 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）
description: 有关从云服务（经典）迁移到云服务（外延支持）的概述
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 1542a8dd6c36c2a623f11179c0871a9fef2e3619
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665504"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>将 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）

本文档概述了如何将云服务（经典）迁移到云服务（外延支持）。     

[云服务（外延支持）](overview.md)的主要优势是，提供区域复原能力，功能与使用 Azure 服务管理器部署的 Azure 云服务相当。 它还提供了一些 Azure 资源管理器功能，例如基于角色的访问控制 (RBAC)、标记、策略，并支持部署模板、专用链接。 两个部署模型（外延支持和经典）均可使用[类似的定价结构](https://azure.microsoft.com/pricing/details/cloud-services/)。

云服务（外延支持）支持以下两种路径，让客户可以从 Azure 服务管理器迁移到 Azure 资源管理器：重新部署和就地迁移。 

下表突出显示了这两个选项之间的比较。  


| 重新部署 | 就地迁移 | 
|---|---|
| 客户可以直接在 Azure 资源管理器中部署新的云服务，然后在 Azure 服务管理器中通过验证删除旧的云服务。 | 就地迁移工具支持将现有云服务（经典）部署经平台协调后无缝地迁移到云服务（外延支持）。 | 
| 客户可通过重新部署执行以下操作： <br><br> - 定义资源名称。 <br><br> - 根据需要组织或重复使用资源。 <br><br> - 以最少的更改重复使用服务配置和定义文件。 | 对于就地迁移，平台可以执行以下操作： <br><br> - 定义资源名称。 <br><br> - 组织单个资源组中的每个部署和相关资源。 <br><br> - 修改 Azure 资源管理器的现有配置和定义文件。 | 
| 客户需要将流量编排到新的部署。 | 迁移会保留 IP 地址，且数据路径不变。 | 
| 客户需要在 Azure 资源管理器中删除旧的云服务。 | 平台在迁移后会删除云服务（经典）资源。 | 
| 这是一种直接迁移，更加灵活但也需要更多时间来进行迁移。 | 这是一种自动迁移，速度更快但灵活性更小。 | 

评估从云服务（经典）到云服务（外延支持）的迁移计划时，可能需要调查其他 Azure 服务，例如：[虚拟机规模集](../virtual-machine-scale-sets/overview.md)、[应用服务](../app-service/overview.md)、[Azure Kubernetes 服务](../aks/intro-kubernetes.md)和 [Azure Service Fabric](../service-fabric/overview-managed-cluster.md)。 这些服务将继续提供其他功能，而云服务（外延支持）将主要保持与云服务（经典）相当的功能。

根据应用程序的不同，与其他选项相比，通过云服务（外延支持）迁移到 Azure 资源管理器所需的工作量可能会少很多。 如果你的应用程序没有不断发展，则云服务（外延支持）是一个可行的选择，因为它提供了一个快速迁移路径。 相反，如果你的应用程序在不断发展并需要更新式的功能集，那么请浏览其他 Azure 服务以更好地满足你当前和未来的需求。

## <a name="redeploy-overview"></a>重新部署概述

使用[云服务（外延支持）](overview.md)重新部署服务有以下好处： 

- 支持 Web 角色和辅助角色，类似于云服务（经典）。
- Web 角色和辅助角色的设计、体系结构或组件没有任何变化。 
- 不需要对运行时代码进行任何更改，因为数据平面和云服务是相同的。 
- Azure GuestOS 发行版和相关更新与云服务（经典）一致。 
- 与更新域相关的基础更新进程、升级如何进行、回退和允许的服务更改在更新期间不会发生变化。

可以使用以下客户端工具直接在 Azure 资源管理器中部署新的云服务（外延支持）：

- [部署云服务 - 门户](deploy-portal.md)
- [部署云服务 - PowerShell](deploy-powershell.md)
- [部署云服务 - 模板](deploy-template.md)
- [部署云服务 - SDK](deploy-sdk.md)
- [部署云服务 - Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>迁移工具概述

平台支持的迁移提供以下重要优势：

- 在大多数情况下，实现平台协调后的无缝迁移无需故障时间。 详细了解[支持的方案](in-place-migration-technical-details.md)。  
- 通过三个简单步骤迁移现有的云服务：验证、准备、提交（或中止）。 详细了解[迁移工具的工作原理](in-place-migration-overview.md#migration-steps)
- 可以在成功准备后测试已迁移的部署。 提交并完成迁移，同时中止回退迁移。

迁移工具利用相同的 API，其体验与[虚拟机（经典）迁移](../virtual-machines/migration-classic-resource-manager-overview.md)相同。 

## <a name="setup-access-for-migration"></a>设置迁移访问权限

若要执行此迁移，必须将你添加为订阅的共同管理员，并注册所需的提供程序。 

1. 登录到 Azure 门户。
3. 在“中心”菜单上中选择“订阅”。  如果看不到该选项，请选择“所有服务”。
3. 查找相应订阅项，然后查看“我的角色”字段。 对于共同管理员，该值应是“帐户管理员”。如果你无法添加共同管理员，请联系订阅的服务管理员或共同管理员，将自己添加为共同管理员。

4. 使用[门户](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) 注册 Microsoft.ClassicInfrastructureMigrate 命名空间的订阅

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. 检查注册状态。 完成注册可能需要几分钟时间。 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>云服务（经典）迁移与虚拟机（经典）迁移有何差别？
Azure Service Manager 支持两种不同的计算产品：[Azure 虚拟机（经典）](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic)和 [Azure 云服务（经典）](../cloud-services/cloud-services-choose-me.md)或 Web/辅助角色。 这两种产品根据云服务中的部署类型而有差异。 Azure 云服务（经典）使用包含具有 Web/辅助角色的部署的云服务。 Azure 虚拟机（经典）使用包含具有 IaaS VM 的部署的云服务。

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

## <a name="supported-configurations--migration-scenarios"></a>支持的配置/迁移方案
这是一些涉及到资源、功能和云服务组合的主要方案。 此列表并未囊括所有方式。

| 服务 | 配置 | 注释 | 
|---|---|---|
| [Azure AD 域服务](../active-directory-domain-services/migrate-from-classic-vnet.md) | 包含 Azure Active Directory 域服务的虚拟网络。 | 支持包含云服务部署和 Azure AD 域服务的虚拟网络。 客户首先需要单独迁移 Azure AD 域服务，然后迁移剩下的仅具有云服务部署的虚拟网络 |
| 云服务 | 仅限在单个槽中具有部署的云服务。 | 可以迁移包含生产槽部署或过渡槽部署的云服务 |
| 云服务 | 不在公开可见的虚拟网络中的部署（默认虚拟网络部署） | 云服务可以位于公开可见的虚拟网络或隐藏的虚拟网络中，也可以不在任何虚拟网络中。  支持迁移位于隐藏虚拟网络和公开可见虚拟网络中的云服务。 客户可以使用“验证”API 来判断部署是否位于默认虚拟网络中，因而可确定该部署是否可迁移。 |
|云服务 | XML 扩展（BGInfo、Visual Studio 调试器、Web 部署和远程调试）。 | 支持迁移所有 XML 扩展 
| 虚拟网络 | 包含多个云服务的虚拟网络。 | 支持迁移包含多个云服务的虚拟网络。 虚拟网络及其包含的所有云服务将一起迁移到 Azure 资源管理器。 |
| 虚拟网络 | 通过门户迁移创建的虚拟网络（需要在 .cscfg 文件中使用“Group Resource-group-name VNet-Name”）  | 在迁移过程中，cscfg 中的虚拟网络名称将更改为使用虚拟网络的 Azure 资源管理器 ID。 (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>若要在迁移后管理部署，请更新 .cscfg 文件的本地副本，以开始使用 Azure 资源管理器 ID 而不是虚拟网络名称。 <br><br>使用旧命名方案的 .cscfg 文件将通不过验证。 
| 虚拟网络 | 迁移在不同子网中具有角色的部署。 | 支持迁移在不同子网中具有不同角色的云服务。 |

## <a name="next-steps"></a>后续步骤
- [平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述](../virtual-machines/migration-classic-resource-manager-overview.md)
- 使用 [Azure 门户](in-place-migration-portal.md)迁移到云服务（外延支持）
- 使用 [PowerShell](in-place-migration-powershell.md) 迁移到云服务（外延支持）
