---
title: 批处理安全性和符合性最佳实践
description: 了解利用您的 Azure Batch 解决方案提高安全性的最佳做法和有用提示。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723806"
---
# <a name="batch-security-and-compliance-best-practices"></a>批处理安全性和符合性最佳实践

本文提供了有关使用 Azure Batch 时增强安全性的指南和最佳实践。

默认情况下，Azure Batch 帐户具有一个公共终结点，并且可公开访问。 创建 Azure Batch 池时，将在 Azure 虚拟网络的指定子网中预配该池。 批处理池中的虚拟机可通过 Batch 创建的公共 IP 地址进行访问。 池中的计算节点可以在需要时彼此通信，例如运行多实例任务，而池中的节点不能与池中的虚拟机进行通信。

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="显示典型批处理环境的关系图。":::

许多功能可帮助您创建更安全的 Azure Batch 部署。 可以通过 [预配无公共 IP 地址的池](batch-pool-no-public-ip-address.md)，限制对节点的访问并减少从 internet 的节点发现。 计算节点可以通过在 [Azure 虚拟网络的子网中预配池](batch-virtual-network.md)来安全地与其他虚拟机或本地网络通信。 并且，你可以从 Azure 专用链接支持的服务 [中启用对虚拟网络的专用访问](private-connectivity.md) 。

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="显示更安全的批处理环境的关系图。":::

## <a name="general-security-related-best-practices"></a>与安全相关的常规最佳做法

### <a name="pool-configuration"></a>池配置

许多安全功能仅适用于使用 [虚拟机配置](nodes-and-pools.md#configurations)配置的池，不适用于使用云服务配置的池。 建议尽可能使用虚拟机 [规模集](../virtual-machine-scale-sets/overview.md)的虚拟机配置池。

### <a name="batch-account-authentication"></a>Batch 帐户身份验证

Batch 帐户访问支持两种身份验证方法：共享密钥和 [Azure Active Directory (Azure AD) ](batch-aad-auth.md)。

强烈建议使用 Azure AD 进行批处理帐户身份验证。 某些批处理功能需要此身份验证方法，其中包括本文中所述的许多安全相关功能。

### <a name="batch-account-pool-allocation-mode"></a>Batch 帐户池分配模式

创建批处理帐户时，可以在两个 [池分配模式](accounts.md#batch-accounts)之间进行选择：

- **Batch service**：默认选项，在此选项中，将在内部订阅中创建用于分配和管理池节点的基础云服务或虚拟机规模集资源，并且不会在 Azure 门户中直接显示。 只有 Batch 池和节点可见。 
- **用户订阅**：基础云服务或虚拟机规模集资源是在与 Batch 帐户相同的订阅中创建的。 因此，除了相应的批处理资源之外，还可以在订阅中查看这些资源。

使用用户订阅模式，在创建池时，将直接在订阅中创建 Batch Vm 和其他资源。 如果要使用 Azure 保留 VM 实例创建批处理池、使用虚拟机规模集资源上的 Azure 策略和/或管理订阅上的核心配额 (在订阅) 中的所有批处理帐户之间共享，则需要使用用户订阅模式。 若要在用户订阅模式下创建 Batch 帐户，还需将订阅注册到 Azure Batch 中，并将该帐户与 Azure Key Vault 相关联。

## <a name="restrict-network-endpoint-access"></a>限制网络终结点访问

### <a name="batch-network-endpoints"></a>批处理网络终结点

请注意，默认情况下，具有公共 IP 地址的终结点用于与 Batch 帐户、批处理池和池节点进行通信。

### <a name="batch-account-api"></a>Batch 帐户 API

 创建批处理帐户时，将创建一个公共终结点，用于使用 [REST API](/rest/api/batchservice/)为该帐户调用大多数操作。 帐户终结点具有一个使用格式的基 URL `https://{account-name}.{region-id}.batch.azure.com` 。 对批处理帐户的访问是安全的，它与使用 HTTPS 加密的帐户终结点进行通信，并使用共享密钥或 Azure Active Directory (Azure AD) 身份验证对每个请求进行身份验证。

### <a name="azure-resource-manager"></a>Azure 资源管理器

除了特定于批处理帐户的操作外， [管理操作](/rest/api/batchmanagement/) 还适用于单个和多个批处理帐户。 可以通过 Azure 资源管理器访问这些管理操作。

通过 Azure 资源管理器使用 HTTPS 对批处理管理操作进行加密，并使用 Azure AD 身份验证对每个请求进行身份验证。

### <a name="batch-pool-nodes"></a>Batch 池节点

批处理服务与池中每个节点上运行的批处理节点代理进行通信。 例如，服务指示节点代理运行任务、停止任务或获取任务的文件。 与节点代理的通信由一个或多个负载均衡器启用，其中的数量取决于池中的节点数。 负载均衡器会将通信转发到所需的节点，每个节点都按唯一的端口号进行寻址。 默认情况下，负载平衡器具有与其关联的公共 IP 地址。 还可以通过 RDP 或 SSH 远程访问池节点 (默认情况下启用此访问，通过负载均衡器) 进行通信。

### <a name="restricting-access-to-batch-endpoints"></a>限制对批处理终结点的访问 

有多种功能可用于限制对各种批处理终结点的访问，特别是在解决方案使用虚拟网络的情况下。 

#### <a name="use-private-endpoints"></a>使用专用终结点

通过[Azure 专用链接](../private-link/private-link-overview.md)，可通过虚拟网络中的专用终结点访问 Azure PaaS 服务和 azure 托管的客户拥有/合作伙伴服务。 你可以使用 "专用链接"，以限制从虚拟网络或任何对等互连的虚拟网络中对 Batch 帐户的访问。 还可以在本地使用 VPN 或 Azure ExpressRoute 通过专用对等互连访问映射到专用链接的资源。

若要使用专用终结点，需要在创建批处理帐户时对其进行适当的配置;必须禁用公共网络访问配置。 创建后，可以创建私有终结点，并将其与 Batch 帐户相关联。 有关详细信息，请参阅 [将专用终结点用于 Azure Batch 帐户](private-connectivity.md)。

#### <a name="create-pools-in-virtual-networks"></a>在虚拟网络中创建池

批处理池中的计算节点可以相互进行通信，例如运行多实例任务，而无需虚拟网络 (VNET) 。 但是，默认情况下，池中的节点不能与虚拟网络上池之外的虚拟机通信，并具有专用 IP 地址，如许可证服务器或文件服务器。

若要允许计算节点与其他虚拟机或本地网络进行安全通信，你可以将池配置为位于 Azure VNET 的子网中。

当池具有公共 IP 终结点时，子网必须允许来自批处理服务的入站通信，才能在计算节点上计划任务和执行其他操作，并根据工作负荷的需要，与 Azure 存储或其他资源通信的出站通信。 对于虚拟机配置中的池，Batch 会在附加到计算节点的网络接口级别 (Nsg) 添加网络安全组。 这些 Nsg 具有要启用的规则：

- 来自批处理服务 IP 地址的入站 TCP 流量
- 远程访问的入站 TCP 流量
- 任何端口到虚拟网络的出站流量 (可能会根据子网级别的 NSG 规则修正) 
- 任何端口上的出站流量 (可能按照子网级别的 NSG 规则进行修改) 

无需在虚拟网络子网级别指定 Nsg，因为 Batch 会配置自己的 Nsg。 如果有一个与部署了 Batch 计算节点的子网关联的 NSG，或者如果想要应用自定义 NSG 规则来替代应用的默认值，则必须至少使用入站和出站安全规则配置此 NSG，以允许对池节点进行 Batch 服务通信，并将池节点与 Azure 存储通信。

有关详细信息，请参阅 [在虚拟网络中创建 Azure Batch 池](batch-virtual-network.md)。

#### <a name="create-pools-with-static-public-ip-addresses"></a>创建具有静态公共 IP 地址的池

默认情况下，与池关联的公共 IP 地址是动态的;它们是在创建池时创建的，并且在调整池大小时可以添加或删除 IP 地址。 当在池节点上运行的任务应用程序需要访问外部服务时，可能需要将对这些服务的访问限制为特定 Ip。  在这种情况下，具有动态 IP 地址将不受管理。

你可以在创建池之前创建与 Batch 帐户相同的订阅中的静态公共 IP 地址资源。 然后，你可以在创建池时指定这些地址。

有关详细信息，请参阅[使用指定的公用 IP 地址创建 Azure Batch 池](create-pool-public-ip.md)。

#### <a name="create-pools-without-public-ip-addresses"></a>创建没有公共 IP 地址的池

默认情况下，将为 Azure Batch 虚拟机配置池中的所有计算节点分配一个或多个公共 IP 地址。 Batch 服务使用这些终结点来计划任务和与计算节点通信，包括对 internet 的出站访问。

若要限制对这些节点的访问并减少从 internet 的这些节点的发现，可以预配无公共 IP 地址的池。

有关详细信息，请参阅 [创建没有公共 IP 地址的池](batch-pool-no-public-ip-address.md)。

#### <a name="limit-remote-access-to-pool-nodes"></a>限制对池节点的远程访问

默认情况下，Batch 允许具有网络连接的节点用户使用 RDP 或 SSH 连接到批处理池中的计算节点。

若要限制对节点的远程访问，请使用以下方法之一：

- 将 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 配置为拒绝访问。 适当的网络安全组 (NSG) 将与池关联。
- 创建 [没有公共 IP 地址](batch-pool-no-public-ip-address.md)的池。 默认情况下，无法在 VNet 外部访问这些池。
- 将 NSG 与 VNet 关联，拒绝对 RDP 或 SSH 端口的访问。
- 不要在节点上创建任何用户。 如果没有任何节点用户，则不能进行远程访问。

## <a name="encrypt-data"></a>加密数据

### <a name="encrypt-data-in-transit"></a>加密传输中的数据

与 Batch 帐户终结点 (或通过 Azure 资源管理器) 的所有通信都必须使用 HTTPS。 `https://`连接到 batch 服务时，必须在 api 中指定的批处理帐户 url 中使用。

与 Batch 服务通信的客户端应配置为使用传输层安全性 (TLS) 1.2。

### <a name="encrypt-batch-data-at-rest"></a>加密静态批处理数据

Batch Api 中指定的某些信息（例如帐户证书、作业和任务元数据）和任务命令行在批处理服务存储时自动加密。 默认情况下，此数据使用每个 Batch 帐户特有 Azure Batch 平台托管密钥进行加密。

你还可以使用 [客户管理的密钥](batch-customer-managed-key.md)加密此数据。 [Azure Key Vault](../key-vault/general/overview.md) 用于生成并存储密钥，密钥标识符已注册到 Batch 帐户。

### <a name="encrypt-compute-node-disks"></a>加密计算节点磁盘

默认情况下，Batch 计算节点具有两个磁盘： OS 磁盘和本地临时 SSD。 批处理管理的[文件和目录](files-and-directories.md)位于临时 SSD 上，后者是文件（例如任务输出文件）的默认位置。 Batch 任务应用程序可以使用 SSD 或 OS 磁盘上的默认位置。

为了获得额外的安全性，请使用以下 Azure 磁盘加密功能之一加密这些磁盘：

- [使用平台管理的密钥进行托管磁盘静态加密](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [使用平台托管密钥在主机上加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 磁盘加密](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>从计算节点安全访问服务

批处理节点可以 [安全地访问](credential-access-key-vault.md) 存储在 [Azure Key Vault](../key-vault/general/overview.md)中的凭据和机密，任务应用程序可以使用这些凭据和密码来访问其他服务。 证书用于授予池节点对 Key Vault 的访问权限。

## <a name="governance-and-compliance"></a>管理和符合性

### <a name="compliance"></a>合规性

为了帮助客户在全球范围内跨管控行业和市场满足自己的符合性义务，Azure 维护了一 [系列法规遵从性服务](https://azure.microsoft.com/overview/trusted-cloud/compliance)。 

这些产品/服务基于各种类型的保证，包括正式的认证、证明、验证、授权和由独立的第三方审核公司生成的评估，以及由 Microsoft 生成的合同修正、自我评估和客户指导文档。 查看 [符合性产品/服务的综合概述](https://aka.ms/AzureCompliance) ，确定哪些服务可能与 Batch 解决方案相关。

### <a name="azure-policy"></a>Azure Policy

[Azure 策略](../governance/policy/overview.md) 可帮助实施组织标准并大规模评估符合性。 Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。

根据池分配模式和策略应该应用到的资源，将 Azure 策略与 Batch 配合使用：

- 直接使用 Microsoft.Batch/batchAccounts 资源。 可以使用批处理帐户的属性的子集。 例如，策略可以包括有效的批处理帐户区域、允许的池分配模式，以及是否为帐户启用公用网络。
- 使用 virtualMachineScaleSets/资源间接。 具有用户订阅池分配模式的 Batch 帐户可以在 Batch 帐户订阅中创建的虚拟机规模集资源上设置策略。 例如，允许的 VM 大小，并确保某些扩展在每个池节点上运行。

## <a name="next-steps"></a>后续步骤

- 查看 [适用于 Batch 的 Azure 安全基线](security-baseline.md)。
- 阅读更多 [Azure Batch 的最佳实践](best-practices.md)。