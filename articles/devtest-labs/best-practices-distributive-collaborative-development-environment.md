---
title: Azure 开发测试实验室资源的分布式协作开发
description: 提供有关设置分布式协作开发环境以开发“开发测试实验室”资源的最佳做法。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: f24ab6e612762df5ee0a0f869507c51ac9e5f538
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644308"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>有关 Azure 开发测试实验室资源的分布式协作开发的最佳做法
分布式协作开发允许不同的团队或人员开发和维护基本代码。 若要取得成功，开发过程需依赖于创建、共享和集成信息的能力。 可以在 Azure 开发测试实验室中运用这项关键开发原则。 实验室中存在多种类型的资源，这些资源通常分布于企业中的不同实验室之间。 不同类型的资源集中在两个区域：

- 在实验室内部存储的资源（基于实验室）
- 在[连接到实验室的外部存储库](devtest-lab-add-artifact-repo.md)中存储的资源（基于代码存储库）。 

本文档介绍一些可以实现跨多个团队协作和分布，同时在所有级别确保自定义能力和质量的最佳做法。

## <a name="lab-based-resources"></a>基于实验室的资源

### <a name="custom-virtual-machine-images"></a>自定义虚拟机映像
可以使用一个在夜间部署到实验室的通用自定义映像源。 有关详细信息，请参阅[映像工厂](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)与特定的实验室相关，没有分布机制。 实验室成员执行所有的公式开发工作。 

## <a name="code-repository-based-resources"></a>基于代码存储库的资源
有两种不同的功能基于代码存储库：项目和环境。 本文将介绍这些功能，以及如何最有效地设置存储库和工作流，以便能够在组织级别或团队级别自定义可用的项目和环境。  此工作流基于标准的[源代码管理分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc)。 

### <a name="key-concepts"></a>关键概念
项目的源信息包括元数据和脚本。 环境的源信息包括具有任何支持性文件（例如 PowerShell 脚本、DSC 脚本、Zip 文件等）的元数据和资源管理器模板。  

### <a name="repository-structure"></a>存储库结构  
源代码管理 (SCC) 的最常见配置是设置一个多层结构，用于存储实验室中使用的代码文件（资源管理器模板、元数据和脚本）。 具体而言，需要创建不同的存储库来存储不同业务级别管理的资源：   

- 公司范围的资源。
- 业务单位/部门范围的资源
- 特定于团队的资源。

其中的每个级别链接到不同的存储库，该存储库中的主分支必须达到生产质量。 每个存储库中的[分支](/azure/devops/repos/git/git-branching-guidance)用于开发这些特定的资源（项目或模板）。 此结构非常符合开发测试实验室，因为你可以轻松地将多个存储库和多个分支同时连接到组织的实验室。 存储库名称包含在用户界面 (UI) 中，以避免存在相同名称、说明和发布者时造成混淆。
     
下图显示了两个存储库：由 IT 部门维护的公司存储库，以及 R&D 部门维护的部门存储库。

![分布式协作开发环境示例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
这种分层结构既能满足开发需求，又能在主分支中保持较高的质量级别，此外，将多个存储库连接到实验室还能实现更大的灵活性。

## <a name="next-steps"></a>后续步骤    
请参阅以下文章：

- 使用 [Azure 门户](devtest-lab-add-artifact-repo.md)或通过 [Azure 资源管理模板](add-artifact-repository.md)将存储库添加到实验室
- [开发测试实验室项目](devtest-lab-artifact-author.md)
- [开发测试实验室环境](devtest-lab-create-environment-from-arm.md)。
