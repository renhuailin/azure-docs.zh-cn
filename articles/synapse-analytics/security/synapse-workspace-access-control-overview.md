---
title: Azure Synapse 工作区访问控制概述
description: 本文介绍用于控制对 Synapse 工作区的访问的机制及其包含的资源和代码项目。
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: b92603e5aa9f7c42a2e4789353077058b1fc6ea9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730928"
---
# <a name="azure-synapse-access-control"></a>Azure Synapse 访问控制 

本文概述了可用于控制对 Azure Synapse 计算资源和数据的访问的机制。  

## <a name="overview"></a>概述

Azure Synapse 提供全面且精细的访问控制系统，该系统集成： 
- 用于资源管理和访问存储中的数据的 Azure 角色， 
- 用于管理对代码和执行的实时访问的 Synapse 角色， 
- 用于对 SQL 池中的数据的数据平面访问的 SQL 角色，以及 
- 源代码管理的 Git 权限，包括持续集成和部署支持。  

Azure Synapse 角色提供可应用于不同范围的权限集。 这种粒度使得向管理员、开发人员、安全人员和操作员授予适当的访问权限以计算资源和数据变得容易。

通过使用符合人员的工作角色的安全组，可以简化访问控制。 只需从适当的安全组添加和删除用户即可管理访问权限。

## <a name="access-control-elements"></a>访问控制元素

### <a name="creating-and-managing-synapse-compute-resources"></a>创建和管理 Synapse 计算资源

Azure 角色用于控制对以下项的管理： 
- 专用 SQL 池、 
- Apache Spark 池和 
- 集成运行时。 

若要创建这些资源，需要成为资源组的 Azure 所有者或参与者。 若要在资源创建后对其进行管理，需要成为资源组或单个资源的 Azure 所有者或参与者。 

### <a name="developing-and-executing-code-in-synapse"></a>在 Synapse 中开发和执行代码 

Synapse 支持两种开发模型。

- Synapse 实时开发。 在 Synapse Studio 中开发和调试代码，然后发布代码以保存和执行。  Synapse 服务是代码编辑和执行的事实来源。  关闭 Synapse Studio 时，任何未发布的工作将丢失。  
- 启用 Git 的开发。 在 Synapse Studio 中开发和调试代码，并提交对 Git 存储库的工作分支的更改。 一个或多个分支的工作会集成到协作分支中，从该分支将工作发布到服务。 Git 存储库是代码编辑的事实来源，而服务是执行的事实来源。 必须在关闭 Synapse Studio 前，将更改提交到 Git 存储库或发布到服务。 [详细了解](../cicd/continuous-integration-deployment.md)如何将 Synapse Analytics 和 Git 结合使用。

在这两种开发模型中，任何有权访问 Synapse Studio 的用户都可以创建代码项目。 但是，你需要其他权限来将项目发布到服务、读取已发布的项目、向 Git 提交更改、执行代码以及访问受凭据保护的关联数据。

### <a name="synapse-roles"></a>Synapse 角色

Synapse 角色用于控制对 Synapse 服务的访问，该服务支持： 
- 列出已发布的代码项目， 
- 发布代码项目、关联服务和凭据定义，
- 执行使用 Synapse 计算资源的代码或管道，
- 执行访问受凭据保护的关联数据的代码或管道，
- 查看与已发布的代码项目关联的输出，
- 监视计算资源状态，并查看运行时日志。

Synapse 角色可以在工作区范围内或在更精细的范围内分配，以限制授予特定 Synapse 资源的权限。

### <a name="git-permissions"></a>Git 权限

在 Git 模式下使用启用 Git 的开发时，Git 权限控制是否可以读取和提交对代码项目的更改，包括关联的服务和凭据定义。   
   
### <a name="accessing-data-in-sql"></a>访问 SQL 中的数据

使用专用和无服务器 SQL 池时，使用 SQL 权限控制数据平面访问。 

工作区的创建者被指定为工作区上的“Active Directory 管理员”。 创建后，可以将此角色分配给其他用户或 Azure 门户中的安全组。

**无服务器 SQL 池**：Synapse 管理员已被授予对无服务器 SQL 池“Built-in”的 `db_owner` (`DBO`) 权限。 若要授予其他用户访问无服务器 SQL 池的权限，Synapse 管理员需要在每个无服务器池上运行 SQL 脚本。  

**专用 SQL 池**：向工作区创建者和工作区 MSI 授予 Active Directory 管理员权限。  不会自动授予访问专用 SQL 池的权限。 若要授予其他用户或组对专用 SQL 池的访问权限，Active Directory 管理员必须针对每个专用 SQL 池运行 SQL 脚本。

请参阅[如何设置 Synapse 访问控制](./how-to-set-up-access-control.md)，了解在 SQL 池中授予 SQL 权限的 SQL 脚本的示例。  

 ### <a name="accessing-system-managed-data-in-storage"></a>访问存储中系统管理的数据

无服务器 SQL 池和 Apache Spark 表将其数据存储在与工作区关联的 ADLS Gen2 容器中。 用户安装的 Apache Spark 库也由同一存储帐户进行管理。 若要启用这些用例，必须授予用户和工作区 MSI 对此工作区 ADLS Gen2 存储容器的存储 Blob 数据参与者访问权限。  

## <a name="using-security-groups-as-a-best-practice"></a>将安全组用作最佳做法

若要简化访问控制管理，可以使用安全组将角色分配给个人和组。 可以创建安全组以建立组织中需要访问 Synapse 资源或项目的角色或工作职能的镜像。  然后，可以为这些基于角色的安全组分配一个或多个 Azure 角色、Synapse 角色、SQL 权限或 Git 权限。 借助精心挑选的安全组，可以通过将用户添加到相应的安全组轻松地为用户分配所需的权限。 

>[!Note]
>如果使用安全组来管理访问权限，则在更改生效之前，Azure Active Directory 会引入额外的延迟。 

## <a name="access-control-enforcement-in-synapse-studio"></a>Synapse Studio 中的访问控制强制执行

Synapse Studio 将根据你的权限和当前模式以不同的方式运行：
- Synapse 实时模式：Synapse Studio 将阻止你查看已发布的内容、发布内容或采取其他操作（如果你没有所需的权限）。  在某些情况下，它会阻止你创建无法使用或保存的代码项目。 
- Git 模式：如果你具有允许将更改提交到当前分支的 Git 权限，则即使你没有权限将更改发布到实时服务，提交操作也会被允许。  

在某些情况下，即使没有发布或提交权限，也允许创建代码项目。 这允许你执行代码（具有所需的执行权限）。 [详细了解](./synapse-workspace-understand-what-role-you-need.md)常见任务所需的角色。 

如果某项功能在 Synapse Studio 中被禁用，工具提示将指示所需的权限。 使用 [Synapse RBAC 角色指南](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them)查找提供缺少的权限所需的角色。


## <a name="next-steps"></a>后续步骤

- 详细了解 [Synapse RBAC](./synapse-workspace-synapse-rbac.md)
- 详细了解 [Synapse RBAC 角色](./synapse-workspace-synapse-rbac-roles.md)
- 了解[如何设置适用于使用安全组的 Synapse 工作区的访问控制](./how-to-set-up-access-control.md)。
- 了解[如何查看 Synapse RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md)
- 了解[如何管理 Synapse RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md)
