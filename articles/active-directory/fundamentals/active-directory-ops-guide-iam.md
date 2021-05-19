---
title: Azure Active Directory 标识和访问管理操作参考指南
description: 本操作参考指南介绍应采取哪些检查与操作来保护标识和访问管理操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370995"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory 标识和访问管理操作参考指南

[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)的本部分介绍应考虑采取哪些检查与操作来保护和管理标识的生命周期及其分配。

> [!NOTE]
> 这些建议截至发布之日为最新，但可能会随着时间而改变。 组织应持续评估其标识实践，因为 Microsoft 产品和服务会不断发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和流程，这可能并不完全属于某一个推出项目。 设置这些任务对于维护环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 定义创建 Azure 订阅的过程 | 因组织而异 |
| 确定谁获得企业移动性 + 安全性许可证 | IAM 运营团队 |
| 确定谁获得 Microsoft 365 许可证 | 生产力团队 |
| 确定谁获得其他许可证，例如 Dynamics、Visual Studio Codespaces 的许可证 | 应用程序所有者 |
| 分配许可证 | IAM 运营团队 |
| 排查和修正许可证分配错误 | IAM 运营团队 |
| 为 Azure AD 中的应用程序预配标识 | IAM 运营团队 |

查看列表时，你可能发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="assigning-owners-recommended-reading"></a>有关分配所有者的建议阅读内容

- [在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)
- [Azure 中的监管](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>本地标识同步

### <a name="identify-and-resolve-synchronization-issues"></a>识别并解决同步问题

Microsoft 建议充分了解本地环境中可能导致云同步出现问题的问题，并针对这些问题建立良好的基线。 由于 [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) 和 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) 等自动化工具可能会生成大量的误报，因此，我们建议通过清理出错的对象，来识别有 100 天以上未得到解决的同步错误。 长期未解决的同步错误可能生成支持事件。 [排查同步期间的错误](../hybrid/tshoot-connect-sync-errors.md)概述了不同类型的同步错误、导致这些错误的某些可能情况，以及这些错误的可能解决方法。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 同步配置

若要启用所有混合体验、基于设备的安全态势以及与 Azure AD 的集成，需要同步由员工用来登录到其桌面的用户帐户。

如果不同步用户登录到的林，则应更改为从正确的林进行同步。

#### <a name="synchronization-scope-and-object-filtering"></a>同步范围和对象筛选

删除不需要同步的已知对象桶可带来以下操作优势：

- 减少同步错误的来源
- 加速同步周期
- 减少本地携带的“垃圾”，例如，云中不相关本地服务帐户的全局地址列表造成的污染

> [!NOTE]
> 如果你发现导入的许多对象未导出到云中，则应按 OU 或特定的特性进行筛选。

要排除的对象示例包括：

- 云应用程序不使用的服务帐户
- 不会在云方案中使用的组，例如，用于授予对资源的访问权限的组
- 充当要在 Azure AD B2B 协作中表示的外部标识的用户或联系人
- 员工不会从中访问云应用程序的计算机帐户，例如 servers

> [!NOTE]
> 如果在发生某件事（例如，从迁移旧域、公司合并或收购）后为单个用户标识配置了多个帐户，则只应同步用户在日常工作中使用的帐户，例如，他们用于登录到其计算机的帐户。

最好是能够在减少要同步的对象数和规则复杂性之间达到平衡。 通常，OU/容器[筛选](../hybrid/how-to-connect-sync-configure-filtering.md)与到 cloudFiltered 特性的简单特性映射之间的组合是有效的筛选组合。

> [!IMPORTANT]
> 如果在生产环境中使用组筛选，则应改用另一种筛选方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步故障转移或灾难恢复

Azure AD Connect 在预配过程中起到重要作用。 如果同步服务器出于任何原因进入脱机状态，则无法在云中更新对本地的更改，从而导致用户在访问时出现问题。 因此，必须定义一个故障转移策略，便于管理员能够在同步服务器脱机后快速恢复同步。 此类策略分为以下几个类别：

- 在过渡模式下部署 Azure AD Connect 服务器 - 允许管理员通过一个简单的配置开关，将过渡服务器“提升”到生产环境。
- 使用虚拟化 - 如果 Azure AD Connect 部署在虚拟机 (VM) 中，则管理员可以利用其虚拟化堆栈来实时迁移或快速重新部署 VM，因而恢复同步。

如果你的组织缺少用于同步的灾难恢复和故障转移策略，则你应该毫不犹豫地在过渡模式下部署 Azure AD Connect。 同样，如果生产配置与过渡配置不匹配，则应该重新建立 Azure AD Connect 过渡模式的基线，以便与生产配置相匹配（包括软件版本和配置）。

![Azure AD Connect 过渡模式配置的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>保持最新

Microsoft 会定期更新 Azure AD Connect。 保持使用最新版本能够利用每个新版本提供的性能改进、bug 修复和新功能。

如果你的 Azure AD Connect 版本已经落后最新版本六个月以上，则应该升级到最新版本。

#### <a name="source-anchor"></a>源定位点

使用 ms-DS-consistencyguid 作为[源定位点](../hybrid/plan-connect-design-concepts.md)可以更轻松地跨林和域迁移对象，这种迁移活动在发生 AD 域合并/清理以及公司合并、收购和资产剥离后很常见。

如果你当前正在使用 ObjectGuid 作为源定位点，则我们建议改用 ms-DS-ConsistencyGuid 。

#### <a name="custom-rules"></a>自定义规则

使用 Azure AD Connect 自定义规则可以控制本地对象与云对象之间的特性流。 但是，过度使用或滥用自定义规则可能会带来以下风险：

- 提高故障排除的复杂性
- 降低执行跨对象的复杂操作时的性能
- 增大生产服务器与过渡服务器之间出现配置差异的概率
- 如果创建的自定义规则的优先级大于 100（该优先级由内置规则使用），则升级 Azure AD Connect 时的开销将会提高

如果使用过于复杂的规则，则应该调查为何存在这种复杂性，并找到简化的机会。 同样，如果创建的自定义规则的优先级值超过 100，则应该修复这些规则，使它们不存在风险或者不与默认规则集冲突。

滥用自定义规则的示例包括：

- 针对目录中的脏数据采取补偿措施 - 在这种情况下，建议与 AD 团队所有者协作，以修正任务的形式清理目录中的数据，并调整过程以避免重新引入错误数据。
- 对单个用户进行一次性修正 - 我们经常会发现一些离群的特例规则，离群的原因往往在于特定的用户存在问题。
- “CloudFiltering”过度复杂 - 尽管减少对象数是良好的做法，但使用大量同步规则也会造成同步范围过于复杂的风险。 如果除了使用 OU 筛选之外，还使用复杂的逻辑来包含/排除对象，则我们建议在同步范围以外处理此逻辑，并使用一个可以通过简单同步规则传送的简单“cloudFiltered”特性来标记对象。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect Configuration Documenter

可以使用 [Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) 工具来生成 Azure AD Connect 安装文档，以便更好地了解同步配置，自信地采取正确的做法，并知道在应用 Azure AD Connect 的新版本或配置或者在添加或更新自定义同步规则后会发生哪些改变。 该工具的当前功能包括：

- 记录 Azure AD Connect 同步的完整配置。
- 记录两个 Azure AD Connect 同步服务器的任何配置更改，或者与给定配置基线之间的差异。
- 生成 PowerShell 部署脚本，以在不同的服务器之间迁移同步规则的差异或自定义。

## <a name="assignment-to-apps-and-resources"></a>应用和资源分配

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Microsoft 云服务的基于组的许可

Azure Active Directory 通过 Microsoft 云服务的[基于组的许可](./active-directory-licensing-whatis-azure-portal.md)来简化许可证管理。 IAM 以这种方式提供组基础结构，并将这些组的管理权委托给组织中的适当团队。 可通过多种方法设置 Azure AD 中的组成员身份，具体包括：

- 从本地同步 - 组可以来自本地目录。如果组织中已建立组管理过程，并且这些过程在经扩展后可以在 Microsoft 365 分配许可证，则此方法非常合适。

- 基于特性/动态 - 可以根据一个基于用户特性的表达式（例如，Department=“sales”）在云中创建组。 Azure AD 将维护组的成员，使其与定义的表达式保持一致。 将这种类型的组用于许可证分配可以实现基于特性的许可证分配，此分配方法对于其目录中数据质量较高的组织而言非常合适。

- 委托所有权 - 可以在云中创建组，并为其指定所有者。 这样，你便可以为业务所有者（例如协作团队或 BI 团队）赋权，以定义谁应该拥有访问权限。

如果你当前正在使用手动过程向用户分配许可证和组成部分，我们建议实现基于组的许可。 如果当前过程既不监视许可错误，也不监视已分配的许可证与可用的许可证，则你应该定义过程改进措施，以解决许可错误并监视许可分配。

许可证管理的另一个方面是定义应该基于组织中的职能启用的服务计划（许可证的组成部分）。 授予对不必要的服务计划的访问权限可能导致用户在 Office 门户中看到他们未接受过培训的工具或者不应该由他们使用的工具。 这可能会进一步增加支持请求数量和不必要的预配，并导致合规性与监管面临风险（例如，在为可能不允许其共享内容的个人预配 OneDrive for Business 的情况下）。

使用以下准则来为用户定义服务计划：

- 管理员应根据用户的角色（例如，白领员工与车间工人）来定义要为其提供的服务计划“组合”。
- 按群集创建组并使用服务计划分配许可证。
- 还可以选择性地定义一个特性来保存用户的服务套餐。

> [!IMPORTANT]
> Azure AD 中基于组的许可引入了处于许可错误状态的用户的概念。 如果你发现任何许可错误，应立即[识别并解决](../enterprise-users/licensing-groups-resolve-problems.md)任何许可证分配问题。

![自动生成的说明的计算机屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命周期管理

如果你当前使用的工具（例如 [Microsoft Identity Manager](/microsoft-identity-manager/) 或第三方系统）依赖于本地基础结构，则我们建议从现有工具中卸载分配，实现基于组的许可，并基于[组](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups)定义组生命周期管理。 同样，如果现有的过程不考虑新员工或离职的员工，则应该根据动态组部署基于组的许可，并定义组成员身份生命周期。 最后，如果针对缺少生命周期管理的本地组部署基于组的许可，请考虑使用云组来实现委托所有权或基于特性的动态成员身份等功能。

### <a name="assignment-of-apps-with-all-users-group"></a>分配包含“所有用户”组的应用

资源所有者可能认为“所有用户”组仅包含“企业员工”，但实际上它们包含“企业员工”和“来宾”   。 因此，在使用“所有用户”组进行应用程序分配，以及授予对 SharePoint 内容或应用程序等资源的访问权限时应特别小心。

> [!IMPORTANT]
> 如果“所有用户”组已启用并用于条件访问策略、应用分配或资源分配，在你不希望该组包含来宾用户的情况下，请务必[保护该组](../external-identities/use-dynamic-groups.md)。 此外，应该通过创建并分配仅包含“企业员工”的组来修复许可分配。 另一方面，如果你发现“所有用户”组已启用但尚未用于授予对资源的访问权限，请确保组织的操作指导原则有意使用了该组（包含“企业员工”和“来宾”）  。

### <a name="automated-user-provisioning-to-apps"></a>将用户自动预配到应用

[将用户自动预配到应用程序](../app-provisioning/user-provisioning.md)是在多个系统之间创建一致的标识预配、取消预配和生命周期的最佳方式。

如果你当前正在以临时方式预配应用，或者正在使用 CSV 文件、JIT 或者不能处理生命周期管理的本地解决方案等内容/功能，我们建议通过 Azure AD 为支持的应用程序[实现应用程序预配](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)，并为 Azure AD 尚不支持的应用程序定义一致的模式。

![Azure AD 预配服务](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect 差异同步周期基线

必须了解组织中的更改量，确保同步周期不会花费太长时间，可在可预测的时间内完成。

[默认的差异同步](../hybrid/how-to-connect-sync-feature-scheduler.md)频率为 30 分钟。 如果差异同步所需的时间持续超过 30 分钟，或者过渡环境与生产环境的差异同步性能存在明显的差别，则应调查并评审[影响 Azure AD Connect 性能的因素](../hybrid/plan-connect-performance-factors.md)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>有关 Azure AD Connect 故障排除的建议阅读内容

- [使用 IdFix 工具准备目录特性以便与 Microsoft 365 同步](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect：排查同步期间发生的错误](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>总结

安全的标识基础结构体现在五个方面。 此列表可帮助你快速找到并采取必要的措施来保护和管理组织中标识的生命周期及其权利。

- 将所有者分配到关键任务。
- 找出并解决同步问题。
- 定义故障转移策略用于实现灾难恢复。
- 简化应用的许可证和分配管理。
- 将用户自动预配到应用。

## <a name="next-steps"></a>后续步骤

[身份验证管理检查和操作](active-directory-ops-guide-auth.md)入门。