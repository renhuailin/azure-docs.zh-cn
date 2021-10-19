---
title: Purview 网络体系结构和最佳做法
description: 本文提供 Azure Purview 网络体系结构选项的示例并介绍最佳做法。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: b49709a5fb31e597b6a6f85ac0648018ea35a744
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661535"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview 网络体系结构和最佳做法

Azure Purview 是一种平台即服务数据治理解决方案。 

Azure Purview 帐户具有公共终结点，这些终结点可通过 Internet 进行访问，用于连接到 Purview。 但是，所有终结点都会通过 AAD 登录和支持精细访问控制的 RBAC 得到保护。
为了增加一个安全层，你还可以为 Azure Purview 帐户创建专用终结点，以便将 Azure 虚拟网络中的专用 IP 地址分配给 Purview 帐户及其受管理资源。 在用户与 API、Purview Studio 交互或执行扫描和引入的情况下，这会将虚拟网络与 Purview 帐户之间的所有流量限制为通过专用链接传送。 目前，Azure Purview 防火墙为 Purview 帐户的公共终结点提供访问控制。 使用专用终结点时，可以使用防火墙来允许或阻止通过公共终结点进行的所有访问。 

可以根据网络和安全要求，将 Azure Purview 帐户设置并保持为基于以下任一网络选项访问 Purview 基础服务或引入功能： 

- 使用 [Azure 公共终结点](#option-1---use-public-endpoints)。 
- 使用[专用终结点](#option-2---use-private-endpoints)。 
- 使用[专用终结点并允许在同一 Purview 帐户上进行公共访问](#option-3---use-both-private-endpoint-and-public-endpoints)。 

若要了解哪个选项最适合你的环境，我们建议先执行以下操作： 

- 在 Azure Purview 中注册和扫描任何数据源之前，先查看网络拓扑和安全要求。 有关详细信息，请参阅[定义 Azure 网络拓扑](/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology)。 

- [为 PaaS 服务定义网络连接模型](/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services)。 

## <a name="intended-audience"></a>目标受众  

- 数据体系结构团队 
- 网络团队  
- 数据安全团队 

## <a name="why-do-you-need-to-consider-defining-a-networking-design-part-of-azure-purview-architecture"></a>为何需要考虑定义网络设计作为 Azure Purview 体系结构的一部分？ 

使用本最佳做法指南来定义和准备 Azure Purview 的网络环境，以便可以根据网络、连接和安全要求从网络或云中的不同位置访问 Purview 并扫描数据源。

我们已在本指南中阐述了 Azure Purview 的几种最常见网络体系结构方案，但是，用户并不局限于本文档中的选项。 为 Purview 帐户规划网络时，请查看 Purview 的限制。 

## <a name="option-1---use-public-endpoints"></a>选项 1 - 使用公共终结点 

默认情况下，可以通过可从 Internet 访问的公共终结点使用 Azure Purview 帐户。 如果你要满足以下要求，请在 Purview 帐户中允许公用网络： 

- 扫描或连接到 Azure Purview 终结点时不需要任何专用连接。 
- 所有数据源仅为 SaaS 应用程序。 
- 所有数据源都有一个可通过 Internet 访问的公共终结点。 
- 业务用户需要通过 Internet 访问 Purview 帐户和 Purview Studio。 

### <a name="integration-runtime-options"></a>集成运行时选项 

若要在 Purview 帐户防火墙设置为“允许公共访问”时扫描数据源，可以根据[数据源支持性](manage-data-sources.md)使用 Azure 集成运行时和[自承载集成运行时](./manage-integration-runtimes.md)。  

- 可以使用 Azure 集成运行时或自承载集成运行时来扫描 Azure SQL 数据库或 Azure Blob 存储等 Azure 数据源。  

- 建议尽可能使用 Azure 集成运行时来扫描 Azure 数据源，以降低成本和管理开销。 
  
- 若要扫描多个 Azure 数据源，请使用公用网络和 Azure 集成运行时。 以下步骤说明了使用 Azure 集成运行时扫描 Azure 中的数据源时的概要通信流：

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="显示 Azure Purview、运行时和数据源之间的连接流的屏幕截图。"lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. 将使用 Azure 集成运行时从 Purview 数据映射启动手动或自动扫描。 
   
  2. Azure 集成运行时连接到数据源以提取元数据。

  3. 元数据在 Azure Purview 的托管存储中排队，并存储在 Azure Blob 存储中。 

  4. 元数据发送到 Azure Purview 数据映射。 

- 扫描本地和基于 VM 的数据源始终需要使用自承载集成运行时。 这些数据源不支持 Azure 集成运行时。 以下步骤说明了使用自承载集成运行时扫描数据源时的概要通信流：

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="显示 Azure Purview、自承载运行时和数据源之间的连接流的屏幕截图。"lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. 将触发手动或自动扫描。 Azure Purview 连接到 Azure 密钥保管库以检索凭据来访问数据源。
   
  2. 将使用自承载集成运行时从 Purview 数据映射启动扫描。 
   
  3. 虚拟机中的自承载集成运行时服务连接到数据源以提取元数据。

  4. 元数据在自承载集成运行时 VM 内存中进行处理。 元数据在 Azure Purview 的托管存储中排队，然后存储在 Azure Blob 存储中。 

  5. 元数据发送到 Azure Purview 数据映射。 

### <a name="authentication-options"></a>身份验证选项  

在 Azure Purview 中扫描数据源时需要提供凭据，使 Azure Purview 能够使用目标数据源中的 Azure 集成运行时来读取资产的元数据。 使用公用网络时，身份验证选项和要求根据以下因素而异： 

- 数据源类型。 例如，如果数据源是 Azure SQL 数据库，则你需要使用 SQL 身份验证，并且需要对每个数据库拥有 db_datareader 访问权限。 此身份可以是 Azure Active Directory 中已作为 db_datareader 添加到 Azure SQL 数据库的某个用户、Azure Purview 托管标识或服务主体。 如果数据源是 Azure Blob 存储，则你可以使用 Azure Active Directory 中已添加为 Azure 存储帐户的“存储 Blob 数据读取者”角色的 Azure Purview 托管标识或服务主体，或者只是使用存储帐户的密钥。  

- 建议尽可能使用 Azure Purview 托管标识来扫描 Azure 数据源，以减少管理开销。 对于任何其他身份验证类型，需要[在 Azure Purview 中为源身份验证设置凭据](manage-credentials.md)： 

  - 在 Azure 密钥保管库中生成机密。 
  - 在 Azure Purview 中注册密钥保管库。  
  - 在 Azure Purview 中，使用保存在 Azure 密钥保管库中的机密创建新凭据。 

- 在扫描中使用的运行时类型。 目前无法将 Azure Purview 托管标识与自承载集成运行时配合使用。 

### <a name="additional-considerations"></a>其他注意事项  

- 如果你选择使用公共终结点来扫描数据源，则本地或基于 VM 的数据源必须与 Azure 终结点建立出站连接。 

- 自承载集成运行时 VM 必须[与 Azure 终结点建立出站连接](manage-integration-runtimes.md#networking-requirements)。 

- Azure 数据源必须允许公共访问，但是，如果在数据源上启用了服务终结点，请确保允许受信任服务列表中的 Azure 服务访问你的 Azure 数据源。 服务终结点通过最佳路径将流量从 VNet 路由到 Azure。 

## <a name="option-2---use-private-endpoints"></a>选项 2 - 使用专用终结点 

如果需要通过专用连接扫描 Azure 虚拟网络中的 Azure IaaS 和 PaaS 数据源以及本地数据源，或者要允许虚拟网络上的用户通过[专用链接](../private-link/private-link-overview.md)安全访问 Azure Purview，可对 Azure Purview 帐户使用 [Azure 专用终结点](../private-link/private-endpoint-overview.md)。 

与其他平台即服务解决方案类似，Azure Purview 服务不支持直接部署到虚拟网络中，因此你无法将某些网络功能用于该产品/服务的资源，例如网络安全组、路由表，或 Azure 防火墙等其他网络相关设备。 可以改为使用可在虚拟网络上启用的专用终结点，并可以禁用公共 Internet 访问以安全连接到 Purview。 

如果需要满足以下任何要求，则必须对 Azure Purview 帐户使用专用终结点： 

- 需要为 Purview 帐户和数据源实现端到端的网络隔离。 

- 需要[阻止公开访问](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access)你的 Purview 帐户。 

- PaaS 数据源是使用专用终结点部署的，并且你已阻止通过公共终结点进行的所有访问。 

- 本地或 IaaS 数据源无法访问公共终结点。 

### <a name="design-considerations"></a>设计注意事项  

- 若要以私密且安全的方式连接到你的 Purview 帐户，需要部署一个帐户和一个门户专用终结点。 （例如，如果你打算通过 API 连接到 Purview，或要启动 Azure Purview Studio）。 

- 如果需要使用专用终结点连接到 Purview Studio，则必须部署帐户和门户专用终结点。 

- 若要通过专用连接来扫描数据源，则需要为 Purview 配置至少一个帐户和一个引入专用终结点。 必须使用除 Azure Purview 托管标识以外的身份验证方法通过自承载集成运行时配置扫描。 

- 在设置任何扫描之前，请查看[通过引入专用终结点扫描数据源的支持矩阵](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint)。

- 查看 [DNS 要求](catalog-private-link-name-resolution.md#deployment-options)。 如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Purview 帐户终结点的 FQDN 解析为专用终结点 IP 地址。 

### <a name="integration-runtime-options"></a>集成运行时选项 

- 如果数据源位于 Azure 中，则你需要在部署了 Azure Purview 引入专用终结点的同一虚拟网络中部署的 Windows 虚拟机上设置并使用自承载集成运行时。 Azure 集成运行时不会与引入专用终结点配合工作。 

- 若要扫描本地数据源，还可以在本地 Windows 计算机上或 Azure 虚拟网络中的 VM 上安装自承载集成运行时。 

- 将专用终结点与 Azure Purview 配合使用时，需要允许在数据源与部署了 Purview 专用终结点的 Azure 虚拟网络上的自承载集成运行时 VM 之间建立网络连接。  

- 我们建议允许自承载集成运行时自动升级。 确保在 Azure 虚拟网络中或企业防火墙上打开所需的出站规则以允许自动升级。 有关详细信息，请参阅[自承载集成运行时网络要求](manage-integration-runtimes.md#networking-requirements)。

### <a name="authentication-options"></a>身份验证选项  

- 不能使用 Azure Purview 托管标识 (MSI) 通过引入专用终结点扫描数据源。 根据数据源类型使用服务主体、帐户密钥或 SQL 身份验证。

- 确保凭据存储在 Azure 密钥保管库中，并已在 Azure Purview 中注册。

- 需要根据你在 Azure 密钥保管库中创建的每个机密，在 Azure Purview 中创建凭据。 需要为 Azure Purview 的机密至少分配对 Azure 中密钥保管库资源的 get 和 list 访问权限，否则凭据无法在 Azure Purview 帐户中正常工作 。 

### <a name="current-limitations"></a>当前限制 

- 使用专用终结点进行引入时，不支持使用引入专用终结点和自承载集成运行时通过整个订阅或资源组扫描多个 Azure 源，但可以改为单独注册并扫描数据源。 

- 若要了解 Azure Purview 专用终结点相关的限制，请参阅[已知限制](catalog-private-link-troubleshoot.md#known-limitations) 

- 有关专用链接服务的相关限制，请参阅 [Azure 专用链接限制](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)。 

### <a name="private-endpoint-scenarios"></a>专用终结点方案 

#### <a name="scenario-1-single-vnet--single-region"></a>方案 1：单个 VNet – 单个区域  

在此方案中，所有 Azure 数据源、自承载集成运行时 VM 和 Purview 专用终结点都部署在 Azure 订阅的同一 VNet 中。   

如果存在本地数据源，则会通过站点到站点 VPN 或 ExpressRoute 连接来提供与部署了 Azure Purview 专用终结点的 Azure 虚拟网络的连接。 

此体系结构主要适用于小型组织或者开发、测试和概念证明方案。 

  :::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="显示单 VNet 方案中具有专用终结点的 Azure Purview 的屏幕截图。"lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="scenario-2-single-region--multiple-vnets"></a>方案 2：单个区域 – 多个 VNet 

若要将 Azure 中的两个或更多虚拟网络连接在一起，可以使用[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。 

许多客户使用中心辐射型网络体系结构在 Azure 中构建其网络基础结构，其中： 

- 网络共享服务（例如网络虚拟设备、ExpressRoute/VPN 网关或 DNS 服务器）部署在中心虚拟网络 (VNet) 中 
- 支路 VNet 通过 VNet 对等互连来使用这些共享服务。 

在中心辐射型网络体系结构中，可为组织的数据治理团队提供一个包括虚拟网络（中心）的 Azure 订阅，而所有数据服务可以位于其他少量几个订阅中，这些订阅通过 VNet 对等互连或站点到站点 VPN 连接来连接到中心虚拟网络。 在此体系结构中，可以在中心订阅和虚拟网络中部署 Azure Purview 以及一个或多个自承载集成运行时 VM，并从同一区域中的多个订阅注册和扫描其他 VNet 中的数据源。 

自承载集成运行时 VM 必须与引入专用终结点位于同一 VNet 中，但它们可以位于不同的子网中。

  :::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="显示多 VNet 方案中具有专用终结点的 Azure Purview 的屏幕截图。"lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

可以选择在支路虚拟网络中部署额外的自承载集成运行时。 在这种情况下，必须在支路虚拟网络中部署额外的帐户和引入专用终结点。 

#### <a name="scenario-3-multiple-regions--multiple-vnets"></a>方案 3：多个区域 – 多个 VNet

如果数据源分布在多个 Azure 区域的一个或多个 Azure 订阅中，则你可以使用此方案。

出于性能和成本优化的目的，强烈建议在数据源所在的每个区域中部署一个或多个自承载集成运行时 VM。 在这种情况下，需要在自承载集成运行时 VM 所在的区域和 VNet 中为 Azure Purview 帐户部署一个额外的帐户和引入专用终结点。  

如果需要注册和扫描其他区域中的任何 Azure Data Lake Storage (Gen 2) 资源，则数据源所在的区域中需有一个本地的自承载集成运行时 VM。 

  :::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="显示多 VNet 和多区域方案中具有专用终结点的 Azure Purview 的屏幕截图。"lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3---use-both-private-endpoint-and-public-endpoints"></a>选项 3 - 使用专用终结点和公共终结点

如果你的一部分数据源使用专用终结点，同时你需要扫描配置了[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的其他数据源，或要扫描具有可通过 Internet 访问的公共终结点的数据源，则需要使用此选项。

如果你需要满足以下任何要求，可对 Azure Purview 帐户使用专用终结点，并在 Purview 帐户上将“公用网络访问”设置“允许”：
  
- 需要使用引入专用终结点扫描一些数据源，并使用公共终结点或服务终结点扫描一些数据源。

### <a name="integration-runtime-options"></a>集成运行时选项 

- 若要扫描配置了专用终结点的 Azure 数据源，需要在部署了 Azure Purview 引入专用终结点的同一虚拟网络中部署的 Windows 虚拟机上设置并使用自承载集成运行时。 将专用终结点与 Azure Purview 配合使用时，需要允许在数据源与部署了 Purview 专用终结点的 Azure 虚拟网络上的自承载集成运行时 VM 之间建立网络连接。  

- 若要扫描配置为允许公共终结点的 Azure 数据源，可以使用 Azure 集成运行时。 

- 若要扫描本地数据源，还可以在本地 Windows 计算机上或 Azure 虚拟网络中的 VM 上安装自承载集成运行时。 

- 我们建议允许自承载集成运行时自动升级。 确保在 Azure 虚拟网络中或企业防火墙上打开所需的出站规则以允许自动升级。 有关详细信息，请参阅[自承载集成运行时网络要求](manage-integration-runtimes.md#networking-requirements)。

### <a name="authentication-options"></a>身份验证选项  

- 若要扫描配置为允许公共终结点的 Azure 数据源，可以根据数据源类型使用任何身份验证选项。
  
- 使用引入专用终结点扫描配置了专用终结点的 Azure 数据源：
  - 不能使用 Azure Purview 托管标识 (MSI)，而应该根据数据源类型使用服务主体、帐户密钥或 SQL 身份验证。 
  
  - 确保凭据存储在 Azure 密钥保管库中，并已在 Azure Purview 中注册。

  - 需要根据你在 Azure 密钥保管库中创建的每个机密，在 Azure Purview 中创建凭据。 需要为 Azure Purview 的机密至少分配对 Azure 中密钥保管库资源的 get 和 list 访问权限，否则凭据无法在 Azure Purview 帐户中正常工作 。 

## <a name="next-steps"></a>后续步骤
-  [使用专用终结点安全访问 Purview](./catalog-private-link.md)
