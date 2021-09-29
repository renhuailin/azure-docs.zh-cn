---
title: 使用平台即服务 (PaaS) 服务
description: 了解如何在 Azure 开发测试实验室中使用平台即服务 (PaaS) 服务。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8d4bcf98d425e322c18e13ec99b525cf1442bc49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548120"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中使用平台即服务 (PaaS) 服务
通过环境功能在开发测试实验室中支持 PaaS。 Git 存储库中的预配置 Azure 资源管理器模板支持开发测试实验室中的环境。 环境可以同时包含 PaaS 和 IaaS 资源。 利用环境，你可以创建可包含 Azure 资源（例如虚拟机、数据库、虚拟网络和 Web 应用）的复杂系统，可以定制这些资源以协同工作。 通过这些模板，可以利用源代码控制工具以一致方式进行部署并改善环境的管理。 

正确设置的系统允许以下方案： 

- 开发人员具有独立环境和多个环境
- 以异步方式测试不同的配置
- 集成到暂存和生产管道，无需任何模板更改
- 在同一实验室中同时拥有开发计算机和环境可以改善管理和成本报告的简单程度。  

开发测试实验室资源提供程序代表实验室用户创建资源，因此，无需向实验室用户提供额外的权限即可让用户使用 PaaS 资源。 下图显示了Service Fabric 群集在实验室中作为一个环境。

![作为环境的 Service Fabric 群集](./media/create-environment-service-fabric-cluster/cluster-created.png)

有关设置环境的详细信息，请参阅[通过 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。 开发测试实验室包含 Azure 资源管理器模板的公共存储库，可以使用此存储库来创建环境，而无需自行连接到外部 GitHub 源。 有关公共环境的详细信息，请参阅[在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。

在大型组织中，开发团队通常会提供一些环境，如自定义/隔离测试环境。 某些环境可能是由业务部门的所有团队使用，或者由某个区域使用。 IT 小组可能希望提供可以由组织内所有团队使用的环境。  

## <a name="customizations"></a>自定义

#### <a name="sandbox"></a>沙盒 
实验室所有者可以自定义实验室环境，以便将用户的角色从“读者”更改为资源组中的“参与者”。 此功能位于实验室的“配置和策略”下的“实验室设置”页中。  此角色更改允许用户在该环境中添加或删除资源。 如果希望进一步限制访问权限，请使用 Azure 策略。 此功能可让你自定义资源或配置，而无需订阅级别的访问权限。

#### <a name="custom-tokens"></a>自定义令牌
有一些自定义实验室信息是在资源组的外部，这些信息特定于模板可以访问的环境。 以下介绍了部分功能： 

- 实验室网络标识
- 位置
- 在其中存储资源管理器模板的存储帐户 
 
#### <a name="lab-virtual-network"></a>实验室虚拟网络
[将连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)一文介绍了如何修改资源管理器模板以使用 `$(LabSubnetId)` 令牌。 创建环境时，会将 `$(LabSubnetId)` 令牌替换为第一个子网标记，其中的“用于虚拟机创建”选项设置为“true”。  这允许我们的环境可以使用以前创建的网络。 如果要在过渡和生产环境中使用与测试环境中相同的资源管理器模板，请在资源管理器模板参数中使用 `$(LabSubnetId)` 作为默认值。 

#### <a name="environment-storage-account"></a>环境存储帐户
开发测试实验室支持使用[嵌套资源管理器模板](../azure-resource-manager/templates/linked-templates.md)。 [[部署用于测试环境的嵌套 Azure 资源管理器模板](deploy-nested-template-environments.md)一文介绍了如何使用 `_artifactsLocation` 和 `_artifactsLocationSasToken` 令牌在相同文件夹中或者在主模板的嵌套文件夹中创建资源管理器模板的 URI。 有关这两个令牌的详细信息，请参阅 [Azure 资源管理器 - 最佳实践指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)的“部署工件”部分。

## <a name="user-experience"></a>用户体验

## <a name="developer"></a>开发人员
开发人员采用创建 VM 的相同工作流来创建特定环境。 开发人员选择环境与计算机映像，并输入模板所需的必要信息。 拥有环境的每个开发人员都可以部署更改和改进的内部循环调试。 可以随时使用最新模板创建环境。  利用此功能，可以销毁并重新创建环境，可帮助减少由于必须手动创建系统或从故障测试中恢复而导致的停机时间。  

### <a name="testing"></a>测试
开发测试实验室环境允许以异步方式独立测试特定的代码和配置。 常见的做法是在环境中设置来自单个拉取请求的代码，并启动任何自动测试。 自动测试运行并完成后，可以针对特定环境执行任何手动测试。 通常作为 CI/CD 管道的一部分完成这一过程。 

## <a name="management-experience"></a>管理体验

### <a name="cost-tracking"></a>成本跟踪
成本跟踪功能包含不同环境中的 Azure 资源，以此作为总体成本趋势的一部分。 按资源区分的成本不会与环境中的不同资源隔离，而是将环境显示为单一成本。

### <a name="security"></a>安全
使用开发测试实验室正确配置的 Azure 订阅可以[将 Azure 资源的访问方式限定为只能通过实验室访问](devtest-lab-add-devtest-user.md)。 利用环境，实验室所有者可允许用户使用批准的配置访问 PaaS 资源，而不允许用户访问任何其他 Azure 资源。 在实验室用户自定义环境的情境中，实验室所有者可以允许参与者访问权限。 参与者访问权限允许实验室用户添加或删除 Azure 资源，但仅限于在托管资源组中执行。 相比允许用户参与者访问订阅，以上方式可以更轻松地进行跟踪和管理。

### <a name="automation"></a>自动化
自动化是大规模、有效生态系统的关键组件。 要想跨订阅和实验室管理或跟踪多个环境，自动化必不可少。

### <a name="cicd-pipeline"></a>CI/CD 管道
开发测试实验室中的 PaaS 服务专注于通过实验室控制访问权限的部署方式，因此有助于改善 CI/CD 管道。

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- 
- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 开发测试实验室中创建一个包含独立 Service Fabric 群集的环境](create-environment-service-fabric-cluster.md)
- [在 Azure 开发测试实验室中将环境连接到实验室的虚拟网络](connect-environment-lab-virtual-network.md)
- [将环境集成到 Azure DevOps CI/CD 管道中](integrate-environments-devops-pipeline.md)
 
