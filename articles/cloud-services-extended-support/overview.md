---
title: '关于 Azure 云服务 (扩展支持) '
description: 了解服务配置文件的网络配置元素的子元素，该元素指定虚拟网络和 DNS 值。
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8ed21d8689bf5340c1bde0a7f782bb8614f7cf11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700195"
---
# <a name="about-azure-cloud-services-extended-support"></a>关于 Azure 云服务 (扩展支持) 

> [!IMPORTANT]
> 云服务（外延支持）目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

云服务 (扩展支持) 是适用于 [Azure 云服务](https://azure.microsoft.com/services/cloud-services/)产品的新的基于 [azure 资源管理器](../azure-resource-manager/management/overview.md)的部署模型，目前为公共预览版。 云服务 (扩展支持) 具有与使用 Azure Service Manager 部署的 Azure 云服务进行区域恢复和功能奇偶校验的主要好处。 它还提供了一些 ARM 功能，例如基于角色的访问和控制 (RBAC) 、标记、策略和支持部署模板。  

进行这种更改后，云服务的基于 Azure Service Manager 的部署模型将被重命名为 [ (经典) 的云服务 ](../cloud-services/cloud-services-choose-me.md)。 你将保留生成和快速部署 web 和云应用程序和服务的功能。 你将能够根据当前需求扩展你的云服务基础结构，并确保你的应用程序的性能能够保持同步，同时降低成本。  

## <a name="what-does-not-change"></a>什么不更改 
- 创建代码，定义配置，并将其部署到 Azure。 Azure 设置计算环境，运行你的代码，然后监视并维护它。
- 云服务 (扩展支持) 还支持两种类型的角色： [web 和辅助](../cloud-services/cloud-services-choose-me.md)角色。 Web 角色和辅助角色的设计、体系结构或组件不会有任何变化。 
- 云服务的三个组件：服务定义 () ，服务配置 ( .cscfg) ，并且服务包的 .cspkg ( 在其 [格式](cloud-services-model-and-package.md)中不会有任何变化。 
- 不需要对运行时代码进行任何更改，因为数据平面是相同的，并且控件平面只更改。 
- Azure GuestOS 发行版和相关更新与云服务 (经典) 
- 与更新域相关的基础更新过程，升级如何继续，回滚和允许的服务更改在更新过程中不会发生变化

## <a name="changes-in-deployment-model"></a>部署模型中的更改

需要对服务配置 ( .cscfg) 和服务)  ( 定义进行最少量的更改，以便部署云服务 (扩展支持) 。 不需要对运行时代码进行任何更改。 但是，需要更新部署脚本才能调用基于 Azure 资源管理器的新 Api。 

:::image type="content" source="media/overview-image-1.png" alt-text="Image 显示了 &quot;模板&quot; 部分的 &quot;经典云服务配置&quot;。 ":::

云服务 (经典) 与云服务之间的主要差异 (与部署相关的扩展支持) ： 

- Azure 资源管理器部署使用 [ARM 模板](../azure-resource-manager/templates/overview.md) ，该模板是一个 JAVASCRIPT 对象表示法 (JSON) 文件，用于定义项目的基础结构和配置。 该模板使用声明性语法，使你可以声明要部署的内容，而不需要编写一系列编程命令来进行创建。 在部署云服务 (扩展支持) 时，服务配置和服务定义文件需要与 [ARM 模板](../azure-resource-manager/templates/overview.md) 保持一致。 为此，可以 [手动创建 ARM 模板](deploy-template.md) ，也可以使用 [PowerShell](deploy-powershell.md)、 [门户](deploy-portal.md) 和 [Visual Studio](deploy-visual-studio.md)实现此目的。  

- 客户必须使用 [Azure Key Vault](../key-vault/general/overview.md) 来 [管理云服务 (扩展支持) 中的证书 ](certificates-and-key-vault.md)。 Azure Key Vault 使你能够安全地存储和管理应用程序凭据（例如机密、密钥和证书），并将其存储在一个中心安全的云存储库中。 应用程序可以在运行时向 Key Vault 进行身份验证，以检索凭据。 

- 通过 [Azure 资源管理器](../azure-resource-manager/templates/overview.md) 部署的所有资源都必须位于虚拟网络内。 虚拟网络和子网在 Azure 资源管理器中使用现有的 Azure 资源管理器 Api 创建，需要在部署云服务 (扩展支持) 时在 .cscfg 的 NetworkConfiguration 部分中进行引用。   

- 每个云服务 (扩展支持) 是一个独立的部署。 云服务 (扩展支持) 在单个云服务中不支持多个槽。  
    - <sup>*</sup>可以使用 VIP 交换功能 (扩展支持) 在两个云服务之间进行交换。 若要测试云服务的新版本并对其进行暂存，请将云服务部署 (扩展支持) 并将其标记为可与其他云服务 (扩展支持的 VIP 交换)   

- 域名服务 (DNS) 标签对于云服务 (扩展支持) 是可选的。 在 Azure 资源管理器中，DNS 标签是与云服务关联的公共 IP 资源的属性。 


<sup>*</sup> 云服务的 VIP 交换 (扩展支持) 在公共预览版期间不可用。  

## <a name="migration-to-azure-resource-manager"></a>迁移到 Azure 资源管理器

云服务 (扩展支持) 提供两个路径，可用于从 [azure Service Manager](/powershell/azure/servicemanagement/overview?preserve-view=true&view=azuresmps-4.0.0) 迁移到 [azure 资源管理器](../azure-resource-manager/management/overview.md)。 
1) 客户直接在 Azure 中部署云服务资源管理器，然后在 Azure Service Manager 中删除旧的云服务。 
2) 就地迁移支持将云服务 (经典) 迁移到云服务的最小停机时间， (扩展支持) 。 

### <a name="additional-migration-options"></a>其他迁移选项

从云服务评估迁移计划时 (经典) 到云服务 (扩展支持) ，你可能需要调查其他 Azure 服务，例如： [虚拟机规模集](../virtual-machine-scale-sets/overview.md)、 [应用服务](../app-service/overview.md)、 [azure Kubernetes 服务](../aks/intro-kubernetes.md)和 [azure Service Fabric](../service-fabric/service-fabric-overview.md)。 这些服务将继续提供其他功能，而云服务 (扩展支持) 将主要维护云服务 (经典的功能奇偶校验。 )  

根据应用程序的不同，云服务 (扩展支持) 可能需要与其他选项相比大大减少迁移到 Azure 资源管理器的工作量。 如果你的应用程序未在发展，则云服务 (扩展支持) 是一个可行的选择，因为它提供了一个快速迁移路径。 相反，如果你的应用程序持续发展并需要更新式的功能集，则请浏览其他 Azure 服务以更好地满足你当前和未来的要求。 

## <a name="next-steps"></a>后续步骤
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。