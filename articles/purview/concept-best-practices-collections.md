---
title: Purview 集合体系结构和最佳做法
description: 本文提供 Azure Purview 集合体系结构的示例并介绍最佳做法。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 838730453f5d49efd756abce40faec74513d52b2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390323"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Azure Purview 集合体系结构和最佳做法  

在 Azure Purview 的核心，数据映射是一个平台即服务 (PaaS) 组件，它在你的整个数据产业中保留资产及其元数据的最新映射。 若要生成数据映射，需要注册并扫描数据源。 在组织中，集中或分散的团队可能正在管理和治理数千个数据源。  

Azure Purview 中的[集合](./how-to-create-and-manage-collections.md)支持元数据的有组织性映射。 使用集合可以在层次结构而不是平面结构中管理和维护数据源、扫描和资产。 集合使你可以根据组织计划使用 Azure Purview 治理环境的方式，来生成数据环境的自定义分层模型。

集合还为数据映射中的元数据提供安全边界。 对集合、数据源和元数据的访问是遵循最低特权模型根据 Azure Purview 中的集合层次结构来设置和维护的： 
- 用户拥有完成其作业所需的最少量访问权限。 
- 用户无权访问他们不需要的敏感数据。

## <a name="intended-audience"></a>目标受众

- 数据体系结构团队 
- 数据治理和管理团队
- 数据安全团队

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>为什么需要为 Azure Purview 帐户定义集合和授权模型？ 

考虑在 Azure Purview 中部署集合以满足以下要求： 

- 根据业务要求、数据的地理分布以及数据管理团队、部门或业务职能来组织数据源、分配资产和运行扫描。 

- 通过向相应的集合分配角色，将数据源和资产的所有权委托给相应的团队。 

- 按集合搜索和筛选资产。 
 

## <a name="define-a-collection-hierarchy"></a>定义集合层次结构  

### <a name="design-considerations"></a>设计注意事项  

- 每个 Purview 帐户是使用默认的根集合创建的。 根集合名称与你的 Azure Purview 帐户名称相同。 无法删除根集合。 若要更改根集合的易记名称，可以从 Purview 管理中心更改 Purview 帐户的易记名称。   

- 集合可以保留数据源、扫描、资产和角色分配。

- 一个集合可以有任意数量的子集合。 但每个集合只能有一个父集合。 不能在根集合上部署集合。

- 数据源、扫描和资产只能属于一个集合。 

- Azure Purview 中的集合层次结构最多支持 256 个集合，集合深度最大为 8 级。 这不包括根集合。 

- 根据设计，不能在单个 Purview 帐户中多次注册数据源。 这种体系结构有助于避免将不同级别的访问控制分配到单个数据源的风险。 如果多个团队使用单个数据源的元数据，则你可以在父集合中注册和管理数据源。 然后，可以在每个子集合下创建相应的扫描，使相关资产出现在每个子集合下。

- 即使数据源在较低级别的集合中注册，世系连接和项目也会附加到根集合。

- 运行新扫描时，扫描部署默认将部署在与数据源相同的集合中。 可以选择不同的子集合来运行扫描。 因此，资产将属于该子集合。 

- 目前不允许跨集合移动数据源。 如果需要将数据源移到不同的集合下，需要删除所有资产，从原始集合中删除数据源，并在目标集合下重新注册数据源。

- 如果为用户授予了对源和目标集合的“数据管护者”角色，则允许跨集合移动资产。 

- 目前不允许执行某些操作，例如移动和重命名集合。 

- 如果集合没有任何资产、关联扫描、数据源或子集合，则可以将其删除。

- 如果数据源、扫描和资产存在于 Azure Purview 数据映射中，则它们必须属于某个集合。    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

-->

### <a name="design-recommendations"></a>设计建议 

- 在规划集合结构之前，请查看 [Azure Purview 帐户最佳做法](./deployment-best-practices.md#determine-the-number-of-purview-instances)并定义组织中所需的足够数量的 Purview 帐户。  

- 我们建议根据组织的安全要求以及数据管理和治理结构来设计集合体系结构。 查看本文中建议的[集合原型](#collections-archetypes)。

- 为使将来可伸缩，我们建议在根集合下面为组织创建一个顶级集合。 在顶级集合（而不是根集合）上分配相关角色。  

- 在 Azure Purview 中生成集合时，请考虑将安全和访问管理纳入设计决策过程。 

- 每个集合都有一个名称属性和一个易记名称属性。 如果你使用 Azure [Purview Studio](https://web.purview.azure.com/resource/) 部署集合，则系统会自动为集合分配由六个字母构成的随机名称，以避免重复。 为了降低复杂性，请避免为不同的集合（尤其是同一级别的集合）使用重复的易记名称。  

- 如果可以的话，请避免将组织结构复制到深层嵌套的集合层次结构中。 如果无法避免这一点，请确保为层次结构中的每个集合使用不同的名称，使集合易于区分。

- 如果你打算批量部署集合和角色分配，请使用 API 来自动部署集合。

- 使用专用服务主体名称 (SPN) 通过 API 对集合和角色分配运行操作。 使用 SPN 可减少拥有提升的权限的用户数，有助于遵循最小特权准则。

## <a name="define-an-authorization-model"></a>定义授权模型

Azure Purview 数据平面角色在 Azure Purview 中进行管理。 部署 Purview 帐户后，将自动为 Purview 帐户的创建者分配根集合的以下角色。 可以在 Azure Purview 中使用 [Purview Studio](https://web.purview.azure.com/resource/) 或编程方法直接分配和管理角色。

  - 集合管理员可以编辑 Purview 集合及其详细信息，并可以添加子集合。 他们还可以将用户添加到他们充当管理员的集合中的其他 Purview 角色。
  - 数据源管理员可以管理数据源和数据扫描。
  - 数据管护者可以创建、读取、修改和删除目录数据资产，以及在资产之间建立关系。
  - 数据读取者可以访问（但不能修改）目录数据资产。

### <a name="design-considerations"></a>设计注意事项  

- Azure Purview 访问权限管理已移到数据平面。 不再使用 Azure 资源管理器角色，因此你应使用 Azure Purview 来分配角色。 

- 在 Azure Purview 中，可以从部署了 Purview 帐户的同一 Azure AD 租户上的 Azure Active Directory (Azure AD) 向用户、安全组和服务主体（包括托管标识）分配角色。
  
- 必须先将来宾帐户作为 B2B 用户添加到 Azure AD 租户，然后才能将 Purview 角色分配给外部用户。 

- 默认情况下，集合管理员无权读取或修改资产。 但他们可以提升自己的访问权限，并将自己添加到其他角色。

- 默认情况下，所有角色分配会自动由所有子集合继承。 但是，可以对除根集合以外的其他任何集合启用“限制继承权限”。 “限制继承权限”会从所有父集合中删除继承的角色（集合管理员角色除外）。 

- 对于 Azure 数据工厂连接：若要连接到 Azure 数据工厂，必须是根集合的集合管理员。

- 如果需要连接到 Azure 数据工厂以获取世系功能，请在 Purview 根集合级别向数据工厂的托管标识授予数据管护者角色。 在创作 UI 中将数据工厂连接到 Purview 时，数据工厂会尝试自动添加这些角色分配。 如果你拥有 Purview 根集合的集合管理员角色，此操作将正常完成。 

### <a name="design-recommendations"></a>设计建议 

- 考虑在 Azure Purview 根集合级别为集合管理员角色实现[紧急访问](/azure/active-directory/users-groups-roles/directory-emergency-access)或急救策略，以避免发生 Purview 帐户级别的锁定。 记录紧急帐户的使用过程。 

    > [!NOTE]
    > 在某些情况下，可能需要使用紧急帐户登录到 Azure Purview。 当其他人都无法登录到 Purview 或其他管理员由于公司身份验证问题而无法完成某些操作时，你可能需要使用此类帐户来解决组织级别的访问问题。 强烈建议遵循有关使用仅限云的用户实现[紧急访问帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)的 Microsoft 最佳做法。
    >
    > 如果以前的集合管理员不可用，请按照[此文](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin)中的说明恢复对 Purview 根集合的访问。

- 尽量减少根集合管理员数量。 在根集合上最多分配三个集合管理员用户，包括 SPN 和急救帐户。 改为将集合管理员角色分配到顶级集合或子集合。

- 将角色分配到组而不是单个用户，以降低管理开销，减少管理个人角色时发生的错误。 

- 在根集合上分配服务主体以实现自动化。

- 为了提高安全性，请至少为集合管理员、数据源管理员和数据管护者启用使用多重身份验证的 Azure AD 条件访问。 确保从条件访问策略中排除紧急帐户。
 
## <a name="collections-archetypes"></a>集合原型

可以基于集中式、分散式或混合数据管理和治理模型部署 Azure Purview 集合。 请根据业务和安全要求做出此决策。

### <a name="example-1-single-region-organization"></a>示例 1：单区域组织 

此结构适用于具有以下特征的组织： 
- 主要在单个地理位置运营。 
- 有一个集中式数据管理和治理团队，其中的下一级数据管理由部门、团队或项目小组负责。  

集合层次结构由以下垂直分支组成： 

- 根集合（默认）
- Contoso（顶级集合）
- 部门（每个部门的委托集合） 
- 团队或项目（根据项目进一步分离）

每个数据源在其相应的集合中进行注册和扫描。 因此，资产也出现在同一个集合中。 

组织级共享数据源在 Hub-Shared 集合中进行注册和扫描。 

部门级共享数据源在部门集合中进行注册和扫描。 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="显示第一个 Azure Purview 集合示例的屏幕截图。"lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>示例 2：多区域组织

此方案适用于具有以下特征的组织： 
- 在多个区域运营。 
- 每个区域的数据治理团队是集中或分散式的。
- 数据管理团队分布在每个地理位置。 

集合层次结构由以下垂直分支组成： 

- 根集合（默认）
- FourthCoffee（顶级收藏）
- 地理位置（基于数据源和数据所有者所在地理位置的中级集合）
- 部门（每个部门的委托集合） 
- 团队或项目（根据团队或项目进一步分离）

在此方案中，每个区域在 Purview 帐户中的顶级集合下都有自己的子集合。 数据源在各自地理位置的相应子集合中进行注册和扫描。 因此，资产也会出现在该区域的子集合层次结构中。 

如果你有集中式数据管理和治理团队，则可以从顶级集合向他们授予访问权限。 这样，他们就可以监督数据映射中的整个数据资产。 集中式团队可以选择性地注册和扫描任何共享数据源。

基于区域的数据管理和治理团队可以从较低级别的相应集合获取访问权限。

部门级共享数据源在部门集合中进行注册和扫描。 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="显示第二个 Azure Purview 集合示例的屏幕截图。"lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>示例 3：多区域，数据转换

如果你想要基于地理位置和数据转换状态来分布元数据访问权限管理，则此方案很有用。 可转换数据以使其更有意义的数据科学家和数据工程师可以管理“原始”和“优化”区域。 然后，他们可以将数据移到“生成”或“管护”区域。  

集合层次结构由以下垂直分支组成： 

- 根集合（默认）
- Fabrikam（顶级集合）
- 地理位置（基于数据源和数据所有者所在地理位置的中级集合）
- 数据转换阶段（原始、优化、生成/管护） 

数据科学家和数据工程师可以在其相应的区域上具有数据管护者角色，以便可以管护元数据。 可以向整个数据角色和业务用户授予对管护区域的数据读取者访问权限。 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="显示第三个 Azure Purview 集合示例的屏幕截图。"lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>示例 4：多区域，业务职能 

需要根据业务职能组织元数据和访问权限管理的组织可以使用此选项。

集合层次结构由以下垂直分支组成： 

- 根集合（默认）
- AdventureWorks（顶级集合）
- 地理位置（基于数据源和数据所有者所在地理位置的中级集合）
- 主要业务职能或客户端（根据职能或客户端进一步分离）

每个区域在 Purview 帐户中的顶级集合下都有自己的子集合。 数据源在各自地理位置的相应子集合中进行注册和扫描。 因此，资产会添加到该区域的子集合层次结构。 

如果你有集中式数据管理和治理团队，则可以从顶级集合向他们授予访问权限。 这样，他们就可以监督数据映射中的整个数据资产。 集中式团队可以选择性地注册和扫描任何共享数据源。

基于区域的数据管理和治理团队可以从较低级别的相应集合获取访问权限。
每个业务单位有自己的子集合。

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="显示第四个 Azure Purview 集合示例的屏幕截图。"lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>访问权限管理选项

如果你要在整个组织中实现数据民主化，请将顶级集合的数据读取者角色分配给数据管理、治理和业务用户。 在子集合级别将数据源管理员和数据管护者角色分配给相应的数据管理和治理团队。

如果需要限制对组织中元数据搜索和发现的访问权限，请在特定的集合级别分配数据读取者和数据管护者角色。 例如，可以限制美国员工，使他们只能读取美国集合级别的数据，而不能读取 LATAM 集合中的数据。 

如果需要实现全面数据民主化（某些集合例外），可以在 Purview 数据映射中应用这两种方案的组合。 可以在顶级集合上分配 Purview 角色，并将继承限制为特定的子集合。

将集合管理员角色分配给顶级集合的集中式数据安全和管理团队。 将更低级别的集合的其他集合管理权限委托给相应的团队。

## <a name="next-steps"></a>后续步骤
-  [在 Purview 中创建集合并分配权限](./quickstart-create-collection.md)
-  [在 Azure Purview 中创建和管理集合](./how-to-create-and-manage-collections.md)
-  [Azure Purview 中的访问控制](./catalog-permissions.md)
