---
title: 利用 Azure Lighthouse 管理大规模 Azure Migrate 项目
description: 了解如何在委派的客户资源上有效使用 Azure Migrate。
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: 8e8ba21881ea5dad36ae640632b6307cd9a22a73
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093602"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>利用 Azure Lighthouse 管理大规模 Azure Migrate 项目

本主题概述了 [Azure Lighthouse](../overview.md) 如何帮助你跨多个 Azure Active Directory (Azure AD) 租户以可伸缩方式使用 [Azure Migrate](../../migrate/migrate-services-overview.md) 。

Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

Azure Migrate 提供了一个集中化中心，用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 通常，为多个客户执行大规模评估和迁移的合作伙伴必须使用 [CSP (云解决方案提供商) 订阅模型](/partner-center/customers-revoke-admin-privileges) 或 [在客户租户中创建来宾用户](../../active-directory/external-identities/what-is-b2b.md)，单独访问每个客户订阅。

使用 Azure Lighthouse 与 Azure Migrate 集成，服务提供商可以大规模发现、评估和迁移不同客户的工作负荷，同时允许客户完全查看和控制其环境。 通过 Azure 委派的资源管理，服务提供商可查看他们跨多个客户租户管理的所有 Azure Migrate 项目。

> [!NOTE]
> 合作伙伴可通过 Azure Lighthouse 为本地 VMware Vm、Hyper-v Vm、物理服务器和 AWS/GCP 实例执行发现、评估和迁移。 [VMWARE VM 迁移](../../migrate/server-migrate-overview.md)有两个选项。 目前，在委派的客户订阅中处理迁移项目时，只能使用基于代理的迁移方法;当前不支持使用无代理复制进行的迁移，因为它是对客户范围的委托访问。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但本指南也适用于 [使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

根据你的方案，你可能希望在客户租户或管理租户中创建 Azure Migrate。 查看以下注意事项，确定哪种模型最适合你的客户的迁移需求。

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>在客户租户中创建 Azure Migrate 项目

使用 Azure Lighthouse 时，一个选项是在客户租户中创建 Azure Migrate 项目。 然后，管理租户中的用户可以在创建迁移项目时选择客户订阅。 从管理租户，服务提供商可以执行必要的迁移操作。 这可能包括部署 Azure Migrate 设备以发现工作负荷、通过对 Vm 进行分组、计算与云相关的成本、查看 VM 准备情况以及执行迁移。

在这种情况下，将不会创建资源并将其存储在管理租户中，即使可以启动并从该租户执行发现和评估步骤。 所有资源（例如迁移项目、本地工作负荷的评估报表以及目标目标上的已迁移资源）都将部署在客户订阅中。 但是，服务提供商可以从他们自己的租户和门户体验中访问所有客户项目。

此方法最大程度地减少了跨多个客户工作的服务提供商的上下文切换，同时让客户将其所有资源保留在自己的租户中。

此模型的工作流将如下所示：

1. 客户 [载入 Azure Lighthouse](onboard-customer.md)。 将与 Azure Migrate 一起使用的标识需要参与方内置角色。 有关使用此角色的示例，请参阅 [azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 示例模板。
1. 指定的用户登录到 Azure 门户中的管理租户，然后转到 Azure Migrate。 此用户 [创建一个 Azure Migrate 项目](../../migrate/create-manage-projects.md)，并选择适当的委托客户订阅。
1. 然后，用户 [执行发现和评估的步骤](../../migrate/tutorial-discover-vmware.md)。

   对于 VMware Vm，在配置设备之前，可以将发现限制为 vCenter Server 数据中心、群集、群集文件夹、主机文件夹或单个 Vm。 若要设置作用域，请对设备用于访问 vCenter Server 的帐户分配权限。 如果在虚拟机监控程序上托管多个客户的 Vm，则此方法很有用。 不能限制 Hyper-v 的发现范围。

    > [!NOTE]
    > 可以通过 Azure Lighthouse 提供的委派访问权限，通过 Azure Migrate 发现和评估 VMware 虚拟机以进行迁移。 对于 VMware 虚拟机的迁移，当前仅支持基于代理的方法在委派的客户订阅中处理迁移项目。

1. 当目标客户订阅准备就绪时，请通过 Azure Lighthouse 授予的访问权限进行迁移。 包含评估结果和迁移资源的迁移项目将在 "目标订阅" 下的 "客户租户" 中创建。

> [!TIP]
> 在迁移之前，需要部署登陆区域来预配基础结构资源，并准备好要将虚拟机迁移到的订阅。 若要访问或创建此登陆区域中的某些资源，可能需要所有者内置角色，这在 Azure Lighthouse 中目前不受支持。 对于这种情况，客户可能需要通过 CSP 订阅模型提供来宾访问角色或委派管理员访问权限。 有关创建多租户登陆区域的方法，请参阅 GitHub 上的 [多租户平台演示解决方案](https://github.com/Azure/Multi-tenant-Landing-Zones) 。

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>在管理租户中创建 Azure Migrate 项目

在此方案中，与迁移相关的操作（如发现和评估）仍由管理租户中的用户执行。 但是，迁移项目和所有相关资源将驻留在合作伙伴租户中，并且如果需要) ，客户将无法直接查看项目 (，但可以与客户共享评估。 每个客户的迁移目标将是客户的订阅。

此方法使服务提供商能够快速启动迁移发现和评估项目，并从客户订阅和租户中提取这些初始步骤。

此模型的工作流将如下所示：

1. 客户 [载入 Azure Lighthouse](onboard-customer.md)。 将与 Azure Migrate 一起使用的标识需要参与方内置角色。 有关使用此角色的示例，请参阅 [azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) 示例模板。
1. 指定的用户登录到 Azure 门户中的管理租户，然后转到 Azure Migrate。 此用户在属于管理租户的订阅中 [创建一个 Azure Migrate 项目](../../migrate/create-manage-projects.md) 。
1. 然后，用户 [执行发现和评估的步骤](../../migrate/tutorial-discover-vmware.md)。 本地 Vm 将在管理租户中创建的迁移项目内发现和评估，然后从此处迁移。

   如果在同一 Hyper-v 主机中管理多个客户，则可以同时发现所有工作负荷。 可以在同一个组中选择客户特定的 Vm，然后可以创建评估，还可以通过选择适当的客户订阅作为目标目标来执行迁移。 无需限制发现范围，你可以在一个迁移项目中维护所有客户工作负荷的完整概述。

1. 准备就绪后，请选择委托的客户订阅作为复制和迁移工作负荷的目标目标，以继续进行迁移。 新创建的资源将存在于客户订阅中，而与迁移项目有关的评估数据和资源将保留在管理租户中。

注意：在部署之前，必须修改参数文件以反映你的环境 https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>用于客户迁移的合作伙伴识别

作为 [Microsoft 合作伙伴网络](https://partner.microsoft.com)的成员，你可以将合作伙伴 ID 链接到用于管理委派的客户资源的凭据。 这允许 Microsoft 根据你为客户执行的任务（包括迁移项目）对你的组织进行属性影响和 Azure 消耗收入。

有关详细信息，请参阅[链接合作伙伴 ID 以跟踪对委派的资源的影响](partner-earned-credit.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Migrate](../../migrate/migrate-services-overview.md)。
- 了解 Azure Lighthouse 支持的其他 [跨租户管理体验](../concepts/cross-tenant-management-experience.md) 。
