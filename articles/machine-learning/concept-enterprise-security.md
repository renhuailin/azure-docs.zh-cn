---
title: 企业安全和管理
titleSuffix: Azure Machine Learning
description: 安全使用 Azure 机器学习：身份验证、授权、网络安全性、数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/22/2021
ms.openlocfilehash: 5a6ee4c0e1a73642323dab67efbfc4b0efe1f1aa
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426956"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure 机器学习的企业安全性和治理

本文介绍可用于 Azure 机器学习的安全和治理功能。 如果管理员、DevOps 和 MLOps 想要创建符合公司策略的安全配置，那么这些功能对其十分有用。 通过 Azure 机器学习和 Azure 平台，你可以：

* 按用户帐户或组限制对资源和操作的访问
* 限制传入和传出的网络通信
* 加密传输中的数据和静态数据
* 扫描漏洞
* 应用和审核配置策略

## <a name="restrict-access-to-resources-and-operations"></a>限制对资源和操作的访问

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 是 Azure 机器学习的标识服务提供程序。 它允许你创建和管理用于向 Azure 资源进行 _身份验证_ 的安全对象（用户、组、服务主体和托管标识）。 如果 Azure AD 已配置为使用多重身份验证，则多重身份验证受支持。

下面是在 Azure AD 中使用多重身份验证的 Azure 机器学习的身份验证过程：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习服务。
1. Azure 机器学习将机器学习服务令牌提供给用户计算目标（例如 Azure 机器学习计算群集）。 运行完成后，用户计算目标使用此令牌回调机器学习服务。 范围限制为工作区。

[![Azure 机器学习中的身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

每个工作区都有一个关联的系统分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)，该标识与工作区同名。 此托管标识用于安全地访问工作区使用的资源。 它对关联的资源具有以下 Azure RBAC 权限：

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |

系统分配的托管标识用于在 Azure 机器学习与其他 Azure 资源之间进行内部的服务到服务身份验证。 用户无法访问该标识令牌，也无法使用它获取对这些资源的访问权限。 用户在具有足够 RBAC 权限的情况下，只能通过 [Azure 机器学习控制和数据平面 API](how-to-assign-roles.md) 来访问这些资源。

托管标识需要资源组（其中包含工作区）的“参与者”权限，才能预配关联的资源并[为 Web 服务终结点部署 Azure 容器实例](how-to-deploy-azure-container-instance.md)。

不建议管理员撤销托管标识对上表中所述资源的访问权限。 可以使用[“重新同步密钥”操作](how-to-change-storage-access-key.md)来恢复访问权限。

> [!NOTE]
> 如果你的 Azure 机器学习工作区具有在 2021 年 5 月 14 日之前创建的计算目标（计算群集、计算实例、Azure Kubernetes 服务等），则你可能还有其他 Azure Active Directory 帐户。 帐户名称以 `Microsoft-AzureML-Support-App-` 开头，并且对你在每个工作区区域的订阅具有参与者级别访问权限。
> 
> 如果工作区未附加任何 Azure Kubernetes 服务 (AKS)，你可以放心删除此 Azure AD 帐户。 
> 
> 如果工作区已附加 AKS 群集，并且这些群集是在 2021 年 5 月 14 日之前创建的，请不要删除此 Azure AD 帐户。 在这种情况下，必须先删除并重新创建 AKS 群集，然后才能删除 Azure AD 帐户。

可以将工作区预配为使用用户分配的托管标识，并将其他角色授予托管标识，以便访问所需目标（例如，访问你自己的 Azure 容器注册表以获取基础 Docker 映像）。 有关详细信息，请参阅[使用托管标识进行访问控制](how-to-use-managed-identities.md)。

还可以将托管标识配置为与 Azure 机器学习计算群集配合使用。 此托管标识独立于工作区托管标识。 使用计算群集时，可以使用托管标识来访问运行训练作业的用户可能无权访问的资源，例如安全数据存储。 有关详细信息，请参阅[对 Azure 上的存储服务进行基于标识的数据访问](how-to-identity-based-data-access.md)。

> [!TIP]
> 在 Azure 机器学习中使用 Azure AD 和 Azure RBAC 有一些例外：
> * 你可以选择启用对计算资源（例如 Azure 机器学习计算实例和计算群集）的 SSH 访问。 SSH 访问基于公钥/私钥对，而不是 Azure AD。 SSH 访问不受 Azure RBAC 管控。
> * 可以使用基于密钥或令牌的身份验证来向部署为 Web 服务（推理终结点）的模型进行身份验证。 密钥为静态字符串，而令牌是通过使用 Azure AD 安全对象来检索的。 有关详细信息，请参阅[如何为部署为 Web 服务的模型配置身份验证](how-to-authenticate-web-service.md)。

有关详细信息，请参阅下列文章：
* [Azure 机器学习工作区的身份验证](how-to-setup-authentication.md)
* [管理对 Azure 机器学习的访问](how-to-assign-roles.md)
* [连接到存储服务](how-to-access-data.md)
* [训练时对机密使用 Azure Key Vault](how-to-use-secrets-in-runs.md)
* [将 Azure AD 托管标识与 Azure 机器学习配合使用](how-to-use-managed-identities.md)
* [将 Azure AD 托管标识与 Web 服务配合使用](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>网络安全性和隔离

若要限制对 Azure 机器学习资源进行网络访问，可以使用 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md)。 VNet 允许你创建与公共 Internet 部分隔离或完全隔离的网络环境。 这会减少解决方案的受攻击面以及数据外泄的几率。

你可以使用虚拟专用网 (VPN) 网关将单个客户端或你自己的网络连接到 VNet

Azure 机器学习工作区可以使用 [Azure 专用链接](../private-link/private-link-overview.md)在 VNet 之后创建专用终结点。 这将提供一组专用 IP 地址，这些地址可用于在 VNet 内访问工作区。 Azure 机器学习依赖的某些服务也可使用 Azure 专用链接，但某些服务依赖于网络安全组或用户定义的路由。

有关详细信息，请参阅以下文档：

* [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [在受保护的虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)
* [配置防火墙](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>数据加密

Azure 机器学习使用 Azure 平台上的各种计算资源和数据存储。 若要详细了解其中每项如何支持静态数据加密和传输中数据加密，请参阅 [Azure 机器学习的数据加密](concept-data-encryption.md)。

将模型部署为 Web 服务时，可以启用传输层安全性 (TLS) 以加密传输中的数据。 有关详细信息，请参阅[配置安全的 Web 服务](how-to-secure-web-service.md)。

## <a name="vulnerability-scanning"></a>漏洞扫描

[Azure 安全中心](../security-center/security-center-introduction.md)对混合云工作负荷提供统一的安全管理和高级威胁防护。 对于 Azure 机器学习，应启用对 [Azure 容器注册表](../container-registry/container-registry-intro.md)资源和 Azure Kubernetes 服务资源的扫描。 有关详细信息，请参阅[通过安全中心扫描 Azure 容器注册表映像](../security-center/defender-for-container-registries-introduction.md)和 [Azure Kubernetes 服务与安全中心的集成](../security-center/defender-for-kubernetes-introduction.md)。

## <a name="audit-and-manage-compliance"></a>审核和管理合规性

[Azure Policy](../governance/policy/index.yml) 是一种管理工具，你可用它来确保 Azure 资源符合你的策略。 可以设置策略以允许或强制实施特定配置，例如 Azure 机器学习工作区是否使用专用终结点。 有关 Azure Policy 的详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。 若要详细了解特定于 Azure 机器学习的策略，请参阅[使用 Azure Policy 审核和管理合规性](how-to-integrate-azure-policy.md)。

## <a name="next-steps"></a>后续步骤

* [Azure 机器学习企业安全最佳做法](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)
* [使用 TLS 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [将 Azure 机器学习与 Azure 防火墙配合使用](how-to-access-azureml-behind-firewall.md)
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-network-security-overview.md)
* [静态数据加密和传输中数据加密](concept-data-encryption.md)
* [在 Azure 上生成实时建议 API](/azure/architecture/reference-architectures/ai/real-time-recommendation)
