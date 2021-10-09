---
title: 审核和管理 Azure 机器学习
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Policy 将内置策略用于 Azure 机器学习，确保工作区符合要求。
author: aashishb
ms.author: aashishb
ms.date: 05/10/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e9a532ca4a4bf87bfb4569c03d367cb4d3d2ea2d
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428229"
---
# <a name="audit-and-manage-azure-machine-learning"></a>审核和管理 Azure 机器学习

团队在 Azure 机器学习上协作时，可能会面临针对资源的配置和组织的不同要求。 机器学习团队可能希望能够灵活地根据协作来组织工作区，或根据用例要求来调整计算群集的大小。 在这些情况下，只有应用程序团队可以管理自己的基础结构，才能最大程度地提高工作效率。

作为平台管理员，你可以使用策略进行护栏布局，使团队可以管理自己的资源。 [Azure Policy](../governance/policy/index.yml) 有助于审核和治理资源状态。 本文介绍适用于 Azure 机器学习的审核控制和治理做法。

## <a name="policies-for-azure-machine-learning"></a>Azure 机器学习的策略

[Azure Policy](../governance/policy/index.yml) 是一种管理工具，你可用它来确保 Azure 资源符合你的策略。

Azure 机器学习提供了一组策略，可用于 Azure 机器学习的常见方案。 你可以将这些策略定义分配给现有订阅，也可以将它们作为基础来创建你自己的自定义定义。

下表列出了可以使用 Azure 机器学习进行分配的一部分策略。 有关 Azure 机器学习的内置策略的完整列表，请参阅 [Azure 机器学习的内置策略](../governance/policy/samples/built-in-policies.md#machine-learning)。

| 策略 | 描述 |
| ----- | ----- |
| 客户管理的密钥 | 审核或强制工作区是否必须使用客户管理的密钥。 |
| 专用链接 | 审核或强制工作区是否使用专用终结点与虚拟网络进行通信。 |
| **专用终结点** | 配置应在其中创建专用终结点的 Azure 虚拟网络子网。 |
| 专用 DNS 区域 | 配置要用于专用链接的专用 DNS 区域。 |
| **用户分配的托管标识** | 审核或强制工作区是否使用用户分配的托管标识。 |
| “禁用本地身份验证” | 审核或强制 Azure 机器学习计算资源应禁用本地身份验证方法。 |
| “修改/禁用本地身份验证” | 配置计算资源以禁用本地身份验证方法。 |

可以在不同的范围（如订阅或资源组级别）内设置策略。 有关详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。

## <a name="assigning-built-in-policies"></a>分配内置策略

若要查看与 Azure 机器学习相关的内置策略定义，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中转到“Azure Policy”。
1. 选择“定义”。
1. 对于“类型”，请选择“内置”；对于“类别”，请选择“机器学习” 。

可在此选择策略定义以进行查看。 查看定义时，可使用“分配”链接将策略分配到某个特定范围，并配置策略的参数。 有关详细信息，请参阅[分配策略 - 门户](../governance/policy/assign-policy-portal.md)。

还可以使用 [Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md) 和[模板](../governance/policy/assign-policy-template.md)来分配策略。

## <a name="conditional-access-policies"></a>条件性访问策略

若要控制谁可以访问 Azure 机器学习工作区，请使用 Azure Active Directory [条件访问](../active-directory/conditional-access/overview.md)。

## <a name="enable-self-service-using-landing-zones"></a>使用登陆区域启用自助服务

登陆区域是一种体系结构模式，用于设置与缩放、治理、安全性和工作效率相关的 Azure 环境。 数据登陆区域是应用程序团队用来托管数据和分析工作负荷的管理员配置环境。

登陆区域的用途是确保团队在 Azure 环境中启动时，所有基础结构配置工作都已完成。 例如，已将安全控制设置为符合组织标准，并已设置网络连接。

使用登陆区域模式，可以让机器学习团队能够以自助方式部署和管理自己的资源。 使用 Azure Policy，你可以作为管理员来审核和管理 Azure 资源的符合性，确保工作区符合你的要求。 

Azure 机器学习与[云采用框架数据管理和分析方案](/azure/cloud-adoption-framework/scenarios/data-management/)中的[数据登陆区域](https://github.com/Azure/data-landing-zone)集成。 此引用实现提供了一个优化的环境（可以将机器学习工作负荷迁移到其中），并包括预配置的 Azure 机器学习策略。

## <a name="configure-built-in-policies"></a>配置内置策略

### <a name="workspace-encryption-with-customer-managed-key"></a>使用客户管理的密钥对工作区进行加密

控制是应使用客户管理的密钥对工作区进行加密，还是应使用 Microsoft 管理的密钥来加密指标和元数据。 若要详细了解如何使用客户管理的密钥，请参阅数据加密文章的 [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) 部分。

若要配置此策略，请将 effect 参数设置为“审核”或“拒绝” 。 如果设置为“审核”，则无需客户管理的密钥即可创建工作区，并在活动日志中创建警告事件。

如果策略设置为“拒绝”，则无法创建工作区，除非该策略指定了客户管理的密钥。 尝试在不使用客户管理的密钥的情况下创建工作区会导致类似于“`Resource 'clustername' was disallowed by policy`”的错误，会在活动日志中创建一个错误。 策略标识符也作为此错误的一部分返回。

### <a name="workspace-should-use-private-link"></a>工作区应使用专用链接

控制工作区是否应使用 Azure 专用链接与 Azure 虚拟网络进行通信。 有关使用专用链接的详细信息，请参阅[为工作区配置专用链接](how-to-configure-private-link.md)。

若要配置此策略，请将 effect 参数设置为“审核”或“拒绝” 。 如果设置为“审核”，则无需使用专用链接即可创建工作区，并在活动日志中创建警告事件。

如果策略设置为“拒绝”，则无法创建工作区，除非该策略使用了专用链接。 尝试不使用专用链接创建工作区会导致错误。 还会在活动日志中记录该错误。 策略标识符将作为此错误的一部分返回。

### <a name="workspace-should-use-private-endpoint"></a>工作区应使用专用链接

配置工作区，以便在 Azure 虚拟网络的指定子网内创建专用终结点。

若要配置此策略，请将 effect 参数设置为 “DeployIfNotExists”。 将 privateEndpointSubnetID 设置为子网的 Azure 资源管理器 ID。

### <a name="workspace-should-use-private-dns-zones"></a>工作区应使用专用 DNS 区域

配置工作区以使用专用 DNS 区域，替代专用终结点的默认 DNS 解析。

若要配置此策略，请将 effect 参数设置为 “DeployIfNotExists”。 将 “privateDnsZoneId” 设置为要使用的专用 DNS 区域的 Azure 资源管理器 ID。 

### <a name="workspace-should-use-user-assigned-managed-identity"></a>工作区应使用用户分配的托管标识

控制是使用系统分配的托管标识（默认设置）还是使用用户分配的托管标识创建工作区。 工作区的托管标识用于访问关联的资源，例如 Azure 存储、Azure 容器注册表、Azure Key Vault 和 Azure Application Insights。 有关详细信息，请参阅[将托管标识与 Azure 机器学习配合使用](how-to-use-managed-identities.md)。

若要配置此策略，请将 effect 参数设置为 audit、deny 或 disabled。 如果设置为 audit，则可以在不指定用户分配的托管标识的情况下创建工作区。 将使用系统分配的标识，并在活动日志中创建一个警告事件。

如果将此策略设置为 deny，那么除非在创建过程中提供用户分配的标识，否则无法创建工作区。 尝试在不提供用户分配的标识的情况下创建工作区会导致错误。 系统还会将该错误记录到活动日志中。 策略标识符将作为此错误的一部分返回。

### <a name="disable-local-authentication"></a>禁用本地身份验证

控制 Azure 机器学习计算群集或实例是否应禁用本地身份验证 (SSH)。

若要配置此策略，请将 effect 参数设置为 audit、deny 或 disabled。 如果设置为“audit”，则可以创建启用了 SSH 的计算，并在活动日志中创建警告事件。

如果策略设置为“deny”，则无法创建计算，除非禁用 SSH。 尝试创建启用了 SSH 的计算会导致错误。 还会在活动日志中记录该错误。 策略标识符将作为此错误的一部分返回。

### <a name="modifydisable-local-authentication"></a>修改/禁用本地身份验证

修改任何 Azure 机器学习计算群集或实例创建请求，以禁用本地身份验证 (SSH)。

若要配置此策略，请将 effect 参数设置为“Modify”或“Disabled”。 如果设置“Modify”，则应用策略的范围内的任何计算群集或实例的创建将自动禁用本地身份验证。

## <a name="next-steps"></a>后续步骤

* [Azure Policy 文档](../governance/policy/overview.md)
* [Azure 机器学习的内置策略](policy-reference.md)
* [通过 Azure 安全中心使用安全策略](../security-center/tutorial-security-policy.md)
* [用于数据管理和分析的云采用框架方案](/azure/cloud-adoption-framework/scenarios/data-management/)概述了在云中运行数据和分析工作负荷的注意事项。
* [云采用框架数据登陆区域](https://github.com/Azure/data-landing-zone)提供了在 Azure 中管理数据和分析工作负荷的参考实现。
* [了解如何使用策略将 Azure 专用链接与 Azure 专用 DNS 区域集成](/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale)，以管理工作区和依赖资源的专用链接配置。
