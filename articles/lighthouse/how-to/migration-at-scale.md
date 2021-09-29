---
title: 大规模管理 Azure Migrate 项目
description: Azure Lighthouse 可帮助你跨委派的客户资源有效地使用 Azure Migrate。
ms.date: 09/13/2021
ms.topic: how-to
ms.openlocfilehash: afca0cedb8a79d5eaf3365ded5985f3b1f38052e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652294"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>使用 Azure Lighthouse 大规模管理 Azure Migrate 项目

本主题概述了 [Azure Lighthouse](../overview.md) 如何帮助你跨多个 Azure Active Directory (Azure AD) 租户以可缩放的方式使用 [Azure Migrate](../../migrate/migrate-services-overview.md)。

Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

Azure Migrate 提供了一个集中化中心，用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 通常，为多位客户执行大规模评估和迁移的合作伙伴必须使用 [CSP（云解决方案提供商）订阅模型](/partner-center/customers-revoke-admin-privileges)或[在客户租户中创建来宾用户](../../active-directory/external-identities/what-is-b2b.md)来单独访问每个客户订阅。

Azure Lighthouse 与 Azure Migrate 相集成，使服务提供商可大规模地发现、评估和迁移不同客户的工作负载，同时使客户能够完全了解和控制其环境。 通过 Azure 委派的资源管理，服务提供商可在单一视图中查看他们跨多个客户租户管理的所有 Azure Migrate 项目。

> [!NOTE]
> 通过 Azure Lighthouse，合作伙伴可发现、评估和迁移本地 VMware 虚拟机 (VM)、Hyper-V VM、物理服务器和 AWS/GCP 实例。 [VMware VM 迁移](../../migrate/server-migrate-overview.md)有两种选择。 目前，在委派的客户订阅中处理迁移项目时，只能使用基于代理的迁移方法。而且目前，通过对客户范围的委派访问不支持使用无代理复制进行迁移。

> [!TIP]
> 尽管本主题中提及的是服务提供商和客户，但本指南同样适用于[使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

你可能希望在客户租户或管理租户中创建 Azure Migrate，具体由你的方案而定。 请查看以下注意事项，并确定哪种模型最适合你客户的迁移需求。

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>在客户租户中创建 Azure Migrate 项目

使用 Azure Lighthouse 时，一种选项是在客户租户中创建 Azure Migrate 项目。 然后在创建迁移项目时，管理租户中的用户可选择客户订阅。 服务提供商可从管理租户执行必要的迁移操作。 这可能包括部署 Azure Migrate 设备来发现工作负载、通过对 VM 进行分组并计算与云相关的成本来评估工作负载、查看 VM 的就绪情况以及执行迁移。

在此方案中，即使可从该租户启动并执行发现和评估步骤，也不会创建任何资源，也不会将资源存储在管理租户中。 所有资源（例如迁移项目、本地工作负载的评估报表和目标位置上已迁移的资源）都将部署到客户订阅中。 不过，服务提供商可从他们自己的租户和门户体验中访问所有客户项目。

通过此方法，服务提供商跨多位客户工作时可尽可能少地切换上下文，而且客户可将其所有资源保留在自己的租户中。

此模型的工作流如下所示：

1. 客户[加入到 Azure Lighthouse](onboard-customer.md)。 将与 Azure Migrate 一起使用的标识必须具备“参与者”这一内置角色。 如需使用此角色的示例，请查看 [delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 示例模板。
1. 指定的用户登录到 Azure 门户中的管理租户，然后转到 Azure Migrate。 该用户[创建一个 Azure Migrate 项目](../../migrate/create-manage-projects.md)，并选择适当的委派客户订阅。
1. 然后，该用户[执行发现和评估步骤](../../migrate/tutorial-discover-vmware.md)。

   对于 VMware VM，在配置设备之前，可将发现范围限制为 vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM。 若要设置范围，请对设备用于访问 vCenter Server 的帐户分配权限。 如果在虚拟机监控程序上托管了多位客户的 VM，那么此方法很有用。 无法限制 Hyper-V 的发现范围。

    > [!NOTE]
    > 可通过 Azure Lighthouse 提供的委派访问权限，使用 Azure Migrate 发现和评估要迁移的 VMware 虚拟机。 对于 VMware 虚拟机的迁移，目前仅支持在委派客户订阅中处理迁移项目时使用基于代理的方法。

1. 目标客户订阅准备就绪后，通过 Azure Lighthouse 授予的访问权限继续进行迁移。 包含评估结果和已迁移资源的迁移项目将在目标订阅下的客户租户中进行创建。

> [!TIP]
> 在迁移之前，将需要部署一个登陆区域来预配基础结构资源，并准备好要将虚拟机迁移到的订阅。 若要访问或在此登陆区域中创建一些资源，可能需要内置角色“所有者”，但　Azure Lighthouse 当前不支持该角色。 对于这种情况，客户可能需要通过 CSP 订阅模型提供来宾访问角色或委派管理员访问权限。 若要了解如何创建多租户登陆区域，请查看 GitHub 上的[“多租户登陆区域”演示解决方案](https://github.com/Azure/Multi-tenant-Landing-Zones)。

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>在管理租户中创建 Azure Migrate 项目

在此方案中，与迁移相关的操作（如发现和评估）仍由管理租户中的用户执行。 但是，迁移项目和所有相关资源将驻留在合作伙伴租户中，客户将无法直接查看项目（尽管可根据需要与客户共享评估）。 每位客户的迁移目标都将是该客户的订阅。

此方法使服务提供商能够快速启动迁移发现和评估项目，无需再从客户订阅和租户执行相关初始步骤。

此模型的工作流如下所示：

1. 客户[加入到 Azure Lighthouse](onboard-customer.md)。 将与 Azure Migrate 一起使用的标识必须具备“参与者”这一内置角色。 如需使用此角色的示例，请查看 [delegated-resource-management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 示例模板。
1. 指定的用户登录到 Azure 门户中的管理租户，然后转到 Azure Migrate。 该用户在属于管理租户的订阅中[创建一个 Azure Migrate 项目](../../migrate/create-manage-projects.md)。
1. 然后，该用户[执行发现和评估步骤](../../migrate/tutorial-discover-vmware.md)。 将在管理租户中创建的迁移项目中发现和评估本地 VM，然后从这里进行迁移。

   如果在同一 Hyper-V 主机中管理多位客户，则可同时发现所有工作负载。 可在同一个组中选择客户特定的 VM，然后可创建评估，并可选择相应客户的订阅作为目标位置来执行迁移。 无需限制发现范围，你可在一个迁移项目中维护所有客户工作负载的完整概述。

1. 准备就绪后，选择委派的客户订阅作为复制和迁移工作负载的目标位置来继续迁移。 客户订阅中将显示新创建的资源，而与迁移项目有关的评估数据和资源将保留在管理租户中。

注意：在部署 https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate 之前，必须修改参数文件以反映你的环境

## <a name="partner-recognition-for-customer-migrations"></a>合作伙伴对客户迁移的认可

作为 [Microsoft 合作伙伴网络](https://partner.microsoft.com)的成员，你可将合作伙伴 ID 与用来管理委派的客户资源的凭据进行关联。 这样，Microsoft 就可根据你为客户执行的任务（包括迁移项目）将影响力和 Azure 消费收入计入你的组织。

有关详细信息，请参阅[链接合作伙伴 ID 以跟踪对委派的资源的影响](partner-earned-credit.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Migrate](../../migrate/migrate-services-overview.md)。
- 了解 Azure Lighthouse 支持的其他[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
