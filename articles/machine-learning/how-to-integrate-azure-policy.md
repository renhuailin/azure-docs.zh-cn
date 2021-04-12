---
title: 审核和管理策略合规性
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Policy 将内置策略用于 Azure 机器学习，确保工作区符合要求。
author: aashishb
ms.author: aashishb
ms.date: 03/12/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 21b07130e99ad4fac9a0a9b2d11aca852a1f205f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584306"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>使用 Azure Policy 审核和管理 Azure 机器学习

[Azure Policy](../governance/policy/index.yml) 是一种管理工具，你可用它来确保 Azure 资源符合你的策略。 通过 Azure 机器学习，你可分配以下策略：

* **客户管理的密钥**：审核或强制执行工作区是否必须使用客户管理的密钥。
* **专用链接**：审核或强制工作区是否使用专用终结点与虚拟网络进行通信。
* **专用终结点**：配置应在其中创建专用终结点的 Azure 虚拟网络子网。
* **专用 DNS 区域**：配置要用于专用链接的专用 DNS 区域。

可以在不同的范围（如订阅或资源组级别）内设置策略。 有关详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。

## <a name="built-in-policies"></a>内置策略

Azure 机器学习提供了一组策略，可用于 Azure 机器学习的常见方案。 你可以将这些策略定义分配给现有订阅，也可以将它们作为基础来创建你自己的自定义定义。 有关 Azure 机器学习的内置策略的完整列表，请参阅 [Azure 机器学习的内置策略](../governance/policy/samples/built-in-policies.md#machine-learning)。

若要查看与 Azure 机器学习相关的内置策略定义，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中转到“Azure Policy”。
1. 选择“定义”。
1. 对于“类型”，请选择“内置”；对于“类别”，请选择“机器学习” 。

可在此选择策略定义以进行查看。 查看定义时，可使用“分配”链接将策略分配到某个特定范围，并配置策略的参数。 有关详细信息，请参阅[分配策略 - 门户](../governance/policy/assign-policy-portal.md)。

还可以使用 [Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md) 和[模板](../governance/policy/assign-policy-template.md)来分配策略。

## <a name="workspace-encryption-with-customer-managed-key"></a>使用客户管理的密钥对工作区进行加密

控制是应使用客户管理的密钥对工作区进行加密，还是应使用 Microsoft 管理的密钥来加密指标和元数据。 若要详细了解如何使用客户管理的密钥，请参阅数据加密文章的 [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) 部分。

若要配置此策略，请将 effect 参数设置为“审核”或“拒绝” 。 如果设置为“审核”，则无需客户管理的密钥即可创建工作区，并在活动日志中创建警告事件。

如果策略设置为“拒绝”，则无法创建工作区，除非该策略指定了客户管理的密钥。 尝试在不使用客户管理的密钥的情况下创建工作区会导致类似于“`Resource 'clustername' was disallowed by policy`”的错误，会在活动日志中创建一个错误。 策略标识符也作为此错误的一部分返回。

## <a name="workspace-should-use-private-link"></a>工作区应使用专用链接

控制工作区是否应使用 Azure 专用链接与 Azure 虚拟网络进行通信。 有关使用专用链接的详细信息，请参阅[为工作区配置专用链接](how-to-configure-private-link.md)。

若要配置此策略，请将 effect 参数设置为“审核”或“拒绝” 。 如果设置为“审核”，则无需使用专用链接即可创建工作区，并在活动日志中创建警告事件。

如果策略设置为“拒绝”，则无法创建工作区，除非该策略使用了专用链接。 尝试不使用专用链接创建工作区会导致错误。 还会在活动日志中记录该错误。 策略标识符将作为此错误的一部分返回。

## <a name="workspace-should-use-private-endpoint"></a>工作区应使用专用链接

配置工作区，以便在 Azure 虚拟网络的指定子网内创建专用终结点。

若要配置此策略，请将 effect 参数设置为 “DeployIfNotExists”。 将 “privateEndpointSubnetID” 设置为子网的 Azure 资源管理器 ID。
## <a name="workspace-should-use-private-dns-zones"></a>工作区应使用专用 DNS 区域

配置工作区以使用专用 DNS 区域，替代专用终结点的默认 DNS 解析。

若要配置此策略，请将 effect 参数设置为 “DeployIfNotExists”。 将 “privateDnsZoneId” 设置为要使用的专用 DNS 区域的 Azure 资源管理器 ID。 

## <a name="next-steps"></a>后续步骤

* [Azure Policy 文档](../governance/policy/overview.md)
* [Azure 机器学习的内置策略](policy-reference.md)
* [通过 Azure 安全中心使用安全策略](../security-center/tutorial-security-policy.md)