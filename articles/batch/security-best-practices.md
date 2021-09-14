---
title: Batch 安全性和符合性最佳做法
description: 了解使用 Azure Batch 解决方案增强安全性的最佳做法和有用技巧。
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: f9bfbeb7a89e3ca1bc71001b173926302548a988
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435539"
---
# <a name="batch-security-and-compliance-best-practices"></a>Batch 安全性和符合性最佳做法

本文提供了有关使用 Azure Batch 时增强安全性的指导和最佳做法。

在默认情况下，Azure Batch 帐户具有公共终结点，并且是可以公开访问的。 在创建 Azure Batch 池时，会在 Azure 虚拟网络的某个指定子网中预配该池。 可以通过 Batch 创建的公共 IP 地址访问 Batch 池中的虚拟机。 池中的计算节点可以在需要时（例如，为了运行多实例任务）相互通信，但池中的节点无法与该池外的虚拟机通信。

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="显示典型 Batch 环境的关系图。":::

有许多功能可帮助你创建更安全的 Azure Batch 部署。 通过[预配无公共 IP 地址的池](batch-pool-no-public-ip-address.md)，可以限制对节点的访问，并减少从 Internet 发现这些节点的可能性。 通过[在 Azure 虚拟网络的子网中预配池](batch-virtual-network.md)，计算节点可以安全地与其他虚拟机或本地网络进行通信。 并且，可以从 Azure 专用链接支持的服务启用[从虚拟网络进行专用访问](private-connectivity.md)。

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="显示更安全的 Batch 环境的关系图。":::

## <a name="general-security-related-best-practices"></a>与安全相关的常规最佳做法

### <a name="pool-configuration"></a>池配置

许多安全功能只适用于使用[虚拟机配置](nodes-and-pools.md#configurations)配置的池，而不适用于使用“云服务配置”的池。 建议尽可能使用“虚拟机配置”池，这样的池会利用[虚拟机规模集](../virtual-machine-scale-sets/overview.md)。

### <a name="batch-account-authentication"></a>Batch 帐户身份验证

Batch 帐户访问支持两种身份验证方法：共享密钥和 [Azure Active Directory (Azure AD)](batch-aad-auth.md)。

强烈建议使用 Azure AD 进行 Batch 帐户身份验证。 有些 Batch 功能（包括本文所述的许多安全相关的功能）需要此身份验证方法。 可以使用 [allowedAuthenticationModes](/rest/api/batchmanagement/batch-account/create) 属性将 Batch 帐户的服务 API 身份验证机制限制为 Azure AD。 设置此属性时，将拒绝使用共享密钥身份验证的 API 调用。

### <a name="batch-account-pool-allocation-mode"></a>Batch 帐户池分配模式

在创建 Batch 帐户时，可以在两种[池分配模式](accounts.md#batch-accounts)间进行选择：

- Batch 服务：默认选项，在内部订阅中创建用于分配和管理池节点的基础云服务或虚拟机规模集资源，并且这些资源不会在 Azure 门户中直接显示。 只有 Batch 池和节点是可见的。
- 用户订阅：在与 Batch 帐户相同的订阅中创建基础云服务或虚拟机规模集资源。 因此，除了相应的 Batch 资源外，在订阅中也可以看到这些资源。

如果使用用户订阅模式，在创建池时，会直接在订阅中创建 Batch VM 和其他资源。 如果需要使用 Azure 虚拟机预留实例来创建 Batch 池，需要在虚拟机规模集资源上使用 Azure Policy，并且/或者需要管理订阅上的核心配额（在订阅中所有 Batch 帐户间共享），则需要使用用户订阅模式。 若要在用户订阅模式下创建 Batch 帐户，还需将订阅注册到 Azure Batch 中，并将该帐户与 Azure Key Vault 相关联。

## <a name="restrict-network-endpoint-access"></a>限制网络终结点访问

### <a name="batch-network-endpoints"></a>Batch 网络终结点

请注意，在默认情况下，具有公共 IP 地址的终结点用于与 Batch 帐户、Batch 池和池节点进行通信。

### <a name="batch-account-api"></a>Batch 帐户 API

 在创建 Batch 帐户时，会创建公共终结点，用于使用 [REST API](/rest/api/batchservice/) 为该帐户调用大多数操作。 该帐户终结点具有使用 `https://{account-name}.{region-id}.batch.azure.com` 格式的基 URL。 对 Batch 帐户的访问是安全的，与帐户终结点的通信使用 HTTPS 进行了加密，并且使用共享密钥或 Azure Active Directory (Azure AD) 身份验证对每个请求进行身份验证。

### <a name="azure-resource-manager"></a>Azure 资源管理器

除了特定于 Batch 帐户的操作之外，[管理操作](/rest/api/batchmanagement/)也适用于一个和多个 Batch 帐户。 可以通过 Azure 资源管理器访问这些管理操作。

通过 Azure 资源管理器访问 Batch 管理操作是使用 HTTPS 进行加密的，并且会使用 Azure AD 身份验证对每个请求进行身份验证。

### <a name="batch-pool-nodes"></a>Batch 池节点

Batch 服务与池中每个节点上运行的 Batch 节点代理进行通信。 例如，该服务会指示节点代理运行任务、停止任务，或获取某个任务的文件。 与节点代理的通信由一个或多个负载均衡器启用，其具体数量取决于池中的节点数。 负载均衡器会将通信转发到所需节点，每个节点都按独一无二的端口号寻址。 在默认情况下，负载均衡器具有与其关联的公共 IP 地址。 还可以通过 RDP 或 SSH 远程访问池节点（此访问在默认情况下是启用的，通过负载均衡器进行通信）。

### <a name="restricting-access-to-batch-endpoints"></a>限制对 Batch 终结点的访问 

有多种功能可用于限制对各种 Batch 终结点的访问，尤其是在解决方案使用虚拟网络时。 

#### <a name="use-private-endpoints"></a>使用专用终结点

利用 [Azure 专用链接](../private-link/private-link-overview.md)，可实现通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 Azure 托管的归客户所有的/合作伙伴服务。 可以使用专用链接来限制从虚拟网络中或从任何对等互连的虚拟网络访问 Batch 帐户。 还可以在本地使用 VPN 或 Azure ExpressRoute 通过专用对等互连访问映射到专用链接的资源。

若要使用专用终结点，需要在创建 Batch 帐户时对其进行相应配置；必须禁用公共网络访问配置。 在创建后，可以创建专用终结点，并将其与 Batch 帐户关联。 有关详细信息，请参阅[将专用终结点与 Azure Batch 帐户配合使用](private-connectivity.md)。

#### <a name="create-pools-in-virtual-networks"></a>在虚拟网络中创建池

Batch 池中的计算节点可以相互通信（例如，为了运行多实例任务），而无需虚拟网络 (VNET)。 但是，在默认情况下，池中的节点无法与位于虚拟网络中池的外部并且具有专用 IP 地址的虚拟机（例如许可证服务器或文件服务器）通信。

若要允许计算节点安全地与其他虚拟机或与本地网络进行通信，可将池预配为位于 Azure VNET 的子网中。

在池具有公共 IP 终结点时，该子网必须允许来自 Batch 服务的入站通信，以便能够在计算节点上计划任务并执行其他操作，并且必须允许出站通信，以便能够根据工作负载需求与 Azure 存储或其他资源通信。 对于虚拟机配置中的池，Batch 会在附加到计算节点的网络接口级别添加网络安全组 (NSG)。 这些 NSG 具有用于启用以下项的规则：

- 来自 Batch 服务 IP 地址的入站 TCP 流量
- 远程访问的入站 TCP 流量
- 任何端口上到虚拟网络的出站流量（可能会根据子网级别 NSG 规则进行修正）
- 任何端口上到 Internet 的出站流量（可能会根据子网级别 NSG 规则进行修正）

无需在子网级别指定 NSG，因为 Batch 会配置其自己的 NSG。 如果 NSG 与部署了 Batch 计算节点的子网关联，或者，如果要应用自定义 NSG 规则来替代应用的默认值，则必须为此 NSG 至少配置入站和出站安全规则，以便允许 Batch 服务与池节点通信以及池节点与 Azure 存储通信。

有关详细信息，请参阅[在虚拟网络中创建 Azure Batch 池](batch-virtual-network.md)。

#### <a name="create-pools-with-static-public-ip-addresses"></a>创建具有静态公共 IP 地址的池

在默认情况下，与池关联的公共 IP 地址是动态的；它们是在创建池时创建的，并且在调整池大小时可以添加或删除 IP 地址。 当池节点上运行的任务应用程序需要访问外部服务时，可能需要将对这些服务的访问限制为特定的 IP。  在这种情况下，具有动态 IP 地址将无法进行管理。

可以在创建池之前在与 Batch 帐户相同的订阅中创建静态公共 IP 地址资源。 然后，可以在创建池时指定这些地址。

有关详细信息，请参阅[使用指定的公用 IP 地址创建 Azure Batch 池](create-pool-public-ip.md)。

#### <a name="create-pools-without-public-ip-addresses"></a>创建没有公共 IP 地址的池

在默认情况下，Azure Batch 虚拟机配置池中的所有计算节点都会被分配一个或多个公共 IP 地址。 Batch 服务使用这些地址来计划任务以及与计算节点的通信，包括对 Internet 的出站访问。

若要限制对这些节点的访问，并减少从 Internet 发现这些节点的可能性，可以预配无公共 IP 地址的池。

有关详细信息，请参阅[创建无公共 IP 地址的池](batch-pool-no-public-ip-address.md)。

#### <a name="limit-remote-access-to-pool-nodes"></a>限制对池节点的远程访问

在默认情况下，Batch 允许具有网络连接的节点用户通过使用 RDP 或 SSH 从外部连接到 Batch 池中的计算节点。

若要限制对节点的远程访问，请使用以下方法之一：

- 将 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 配置为拒绝访问。 相应的网络安全组 (NSG) 将与该池关联。
- 创建[无公共 IP 地址](batch-pool-no-public-ip-address.md)的池。 在默认情况下，无法在 VNet 外部访问这些池。
- 将 NSG 与 VNet 关联，以拒绝对 RDP 或 SSH 端口的访问。
- 请勿在节点上创建任何用户。 如果没有任何节点用户，则无法进行远程访问。

## <a name="encrypt-data"></a>加密数据

### <a name="encrypt-data-in-transit"></a>加密传输中的数据

所有到 Batch 帐户终结点（或通过 Azure 资源管理器）的通信都必须使用 HTTPS。 在连接到 Batch 服务时，必须在 API 中指定的 Batch 帐户 URL 中使用 `https://`。

与 Batch 服务通信的客户端应该配置为使用传输层安全性 (TLS) 1.2。

### <a name="encrypt-batch-data-at-rest"></a>加密 Batch 静态数据

Batch API 中指定的有些信息（例如，帐户证书、作业和任务元数据，以及和任务命令行）在由 Batch 服务存储时会自动加密。 在默认情况下，会使用每个 Batch 帐户独有的 Azure Batch 平台管理的密钥加密这些数据。

还可以使用[客户管理的密钥](batch-customer-managed-key.md)来加密这些数据。 [Azure Key Vault](../key-vault/general/overview.md) 用于生成并存储密钥，密钥标识符会注册到 Batch 帐户。

### <a name="encrypt-compute-node-disks"></a>加密计算节点磁盘

在默认情况下，Batch 计算节点具有两个磁盘：OS 磁盘和本地临时 SSD。 Batch 管理的[文件和目录](files-and-directories.md)位于临时 SSD 上，那里是文件（例如任务输出文件）的默认位置。 Batch 任务应用程序可以使用 SSD 或 OS 磁盘上的默认位置。

为了获得额外的安全性，请使用以下某一项 Azure 磁盘加密功能来加密这些磁盘：

- [使用平台管理的密钥进行托管磁盘静态加密](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [在主机上使用平台管理的密钥进行加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 磁盘加密](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>从计算节点安全地访问服务

Batch 节点可以安全地访问 [Azure 密钥保管库](../key-vault/general/overview.md)中存储的凭据，任务应用程序可以使用这些凭据来访问其他服务。 使用证书来授予池节点对 Key Vault 的访问权限。 通过[在 Batch 池中启用自动证书轮换](automatic-certificate-rotation.md)，将自动续订凭据。 建议让 Batch 节点通过这种选项访问存储在 Azure 密钥保管库中的凭据，不过，你也可以[设置 Batch 节点以使用证书安全访问凭据和机密](credential-access-key-vault.md)，而不是自动轮换证书。

## <a name="governance-and-compliance"></a>管理和符合性

### <a name="compliance"></a>合规性

为了帮助客户在全球范围内受监管的行业和市场中履行他们自己的符合性责任，Azure 维护了[一系列法规符合性产品/服务](https://azure.microsoft.com/overview/trusted-cloud/compliance)。

这些产品/服务基于各种类型的保证，包括独立的第三方审核企业生成的正式认证、证明、验证、授权和评估，以及 Microsoft 生成的合同修正、自我评估和客户指南文档。 请查看[符合性产品/服务的综合概述](https://aka.ms/AzureCompliance)，以确定哪些服务可能与你的 Batch 解决方案相关。

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) 有助于强制实施组织标准以及大规模评估符合性。 Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。

根据池分配模式和策略应该应用到的资源，请采用以下某一种方式来将 Azure Policy 与 Batch 配合使用：

- 直接方式，使用 Microsoft.Batch/batchAccounts 资源。 可以使用 Batch 帐户的一部分属性。 例如，策略可以包括有效的 Batch 帐户区域、允许的池分配模式，以及是否为帐户启用了公用网络。
- 间接方式，使用 Microsoft.Compute/virtualMachineScaleSets 资源。 使用用户订阅池分配模式的 Batch 帐户可以在 Batch 帐户订阅中创建的虚拟机规模集资源上设置策略。 例如，允许的 VM 大小，以及确保某些扩展在每个池节点上运行。

## <a name="next-steps"></a>后续步骤

- 查看[适用于 Batch 的 Azure 安全基线](security-baseline.md)。
- 阅读更多 [Azure Batch 的最佳做法](best-practices.md)。
