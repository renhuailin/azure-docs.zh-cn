---
title: 关于 Azure 云服务（外延支持）
description: 了解服务配置文件的 Network Configuration 元素的子元素，该元素指定虚拟网络和 DNS 值。
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ffea4e8edd9cc6c11644017e972db3ce3202c766
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747584"
---
# <a name="about-azure-cloud-services-extended-support"></a>关于 Azure 云服务（外延支持）

云服务（外延支持）是适用于  [Azure 云服务](https://azure.microsoft.com/services/cloud-services/)产品的基于  [Azure 资源管理器](../azure-resource-manager/management/overview.md)的新部署模型，现已正式发布。 云服务（外延支持）的主要优势是，提供区域复原能力，功能与使用 Azure 服务管理器部署的 Azure 云服务相当。 它还提供了一些 ARM 功能，例如基于角色的访问和控制 (RBAC)、标记、策略，并支持部署模板。  

进行这一更改后，云服务的基于 Azure 服务管理器的部署模型将被重命名为[云服务（经典）](../cloud-services/cloud-services-choose-me.md)。 你将保留生成和快速部署 Web 和云应用程序和服务的能力。 你将能够根据当前需求扩展云服务基础结构，并确保应用程序的性能能够保持同步，同时降低成本。  

:::image type="content" source="media/inside-azure-for-iot.png" alt-text="关于云服务（外延支持）的 YouTube 视频。" link="https://youtu.be/H4K9xTUvNdw":::


## <a name="what-does-not-change"></a>未变更的内容 
- 你将创建代码，定义配置，并将其部署到 Azure。 Azure 设置计算环境，运行代码，然后监视并维护它。
- 云服务（外延支持）还支持两种类型的角色：[Web 角色和辅助角色](../cloud-services/cloud-services-choose-me.md)。 Web 角色和辅助角色的设计、体系结构或组件没有任何变化。 
- 云服务的 3 个组件是服务定义 (.csdef)、服务配置 (.cscfg) 和服务包 (.cspkg)，它们将继续使用，而且[格式](cloud-services-model-and-package.md)也不会有任何变化。 
- 不需要对运行时代码进行任何更改，因为数据平面是相同的，仅控制平面发生变更。 
- Azure GuestOS 发行版和相关更新与云服务（经典）一致
- 与更新域相关的基础更新进程，升级如何进行，回滚和允许的服务更改在更新期间不会发生变化

## <a name="changes-in-deployment-model"></a>部署模型中的更改

需要对服务配置 (.cscfg) 和服务定义 (.csdef) 进行少量的更改，以便部署云服务（外延支持）。 不需要对运行时代码进行任何更改。 但是，需要更新部署脚本才能调用基于 Azure 资源管理器的新 API。 

:::image type="content" source="media/overview-image-1.png" alt-text="图像显示了添加了模板部分的经典云服务配置。":::

云服务（经典）与云服务（外延支持）在部署方面的主要区别是： 

- Azure 资源管理器部署使用 [ARM 模板](../azure-resource-manager/templates/overview.md)，该模板是 JavaScript 对象表示法 (JSON) 文件，它定义了项目的基础结构和配置。 该模板使用声明性语法，使你可以指明要部署的内容，而不需要编写一系列编程命令来创建内容。 在部署云服务（外延支持）时，服务配置和服务定义文件需要与 [ARM 模板](../azure-resource-manager/templates/overview.md)保持一致。 为此，可以[手动创建 ARM 模板](deploy-template.md)，也可以使用 [PowerShell](deploy-powershell.md)、[门户](deploy-portal.md)和 [Visual Studio](deploy-visual-studio.md) 实现此目的。  

- 客户必须使用 [Azure Key Vault](../key-vault/general/overview.md) 来[管理云服务（外延支持）中的证书](certificates-and-key-vault.md)。 使用 Azure Key Vault，你可以在一个集中且安全的云存储库中安全地存储和管理应用程序凭据（例如机密、密钥和证书）。 应用程序可以在运行时向 Key Vault 进行身份验证，以检索凭据。 

- 通过 [Azure 资源管理器](../azure-resource-manager/templates/overview.md)部署的所有资源都必须位于虚拟网络内。 虚拟网络和子网在 Azure 资源管理器中使用现有的 Azure 资源管理器 API 创建，需要在部署云服务（外延支持）时在 .cscfg 的 NetworkConfiguration 部分中进行引用。   

- 每个云服务（外延支持）都是一个独立的部署。 云服务（外延支持）在单个云服务中不支持多个槽。  
    - 可以使用 VIP 交换功能在两个云服务（外延支持）之间进行交换。 若要测试云服务的新版本并对其进行暂存，请部署云服务部署（外延支持）并将其标记为可与其他云服务（外延支持）交换的 VIP  

- 域名服务 (DNS) 标签对于云服务（外延支持）是可选的。 在 Azure 资源管理器中，DNS 标签是与云服务关联的公共 IP 资源的属性。 

## <a name="migration-to-azure-resource-manager"></a>迁移到 Azure 资源管理器

云服务（外延支持）提供两个路径，可用于从 [Aazure 服务管理器](/powershell/azure/servicemanagement/overview)迁移到 [Azure 资源管理器](../azure-resource-manager/management/overview.md)。 
1) 客户直接在 Azure 资源管理器中部署云服务，然后在 Azure 服务管理器中删除旧的云服务。 
2) 就地迁移支持将云服务（经典）迁移到云服务（外延支持），只会出现很短的停机时间或没有停机时间。 

### <a name="additional-migration-options"></a>其他迁移选项

评估从云服务（经典）到云服务（外延支持）的迁移计划时，可能需要调查其他 Azure 服务，例如：[虚拟机规模集](../virtual-machine-scale-sets/overview.md)、[应用服务](../app-service/overview.md)、[Azure Kubernetes 服务](../aks/intro-kubernetes.md)和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md)。 这些服务将继续提供其他功能，而云服务（外延支持）将主要保持与云服务（经典）相当的功能。 

根据应用程序的不同，与其他选项相比，通过云服务（外延支持）迁移到 Azure 资源管理器所需的工作量可能会少很多。 如果你的应用程序没有不断发展，则云服务（外延支持）是一个可行的选择，因为它提供了一个快速迁移路径。 相反，如果你的应用程序在不断发展并需要更新式的功能集，那么请浏览其他 Azure 服务以更好地满足你当前和未来的需求。 

## <a name="next-steps"></a>后续步骤
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
