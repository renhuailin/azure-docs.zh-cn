---
title: 在 Azure 上部署 Ethereum 权威证明联盟解决方案模板
description: 在 Azure 上使用 Ethereum 权威证明联盟解决方案部署和配置多成员联盟 Ethereum 网络
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: 58615f1d57c5b97da555e894bcc33dcf98dee204
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705236"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>在 Azure 上部署 Ethereum 权威证明联盟解决方案模板

使用 [Ethereum 权威证明联盟预览版 Azure 解决方案模板](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum)，即使不太了解 Azure 和 Ethereum 知识也能部署、配置和控制多成员协会权威证明 Ethereum 网络。

每个联盟成员均可使用解决方案模板，通过 Microsoft Azure 计算、网络和存储服务来预配区块链网络需求量。 每个联盟成员的网络内存占用由一组负载均衡验证程序节点组成，应用程序或用户可以通过与这些节点交互来提交 Ethereum 事务。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>选择 Azure 区块链解决方案

在选择使用 Ethereum 权威证明联盟解决方案模板之前，请将你的方案与可用 Azure 区块链选项的常见用例进行比较。

> [!IMPORTANT]
> 请考虑使用 [Azure 区块链服务](../service/overview.md)，而不是 Ethereum on Azure 解决方案模板。 Azure 区块链服务是受支持的托管 Azure 服务。 Parity Ethereum 已过渡到社区驱动的开发和维护。 有关详细信息，请参阅[将 Parity Ethereum 过渡到 OpenEthereum DAO](https://www.parity.io/parity-ethereum-openethereum-dao/)。

选项 | 服务模型 | 常见用例
-------|---------------|-----------------
解决方案模板 | IaaS | 解决方案模板是 Azure 资源管理器模板，可用于预配完全配置的区块链网络拓扑。 模板为给定区块链网络类型部署和配置 Microsoft Azure 计算、网络和存储服务。 提供的解决方案模板不含服务级别协议。 使用 [Microsoft Q&A 问题页面](/answers/topics/azure-blockchain-workbench.html)获取支持。
[Azure 区块链服务](../service/overview.md) | PaaS | Azure 区块链服务预览版简化了联盟区块链网络的构成、管理和监管。 将 Azure 区块链服务用于需要 PaaS、联盟管理或合同和事务隐私的解决方案。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 预览版是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 使用 Azure 区块链工作台为区块链解决方案或区块链应用程序概念证明设计原型。 服务级别协议未随 Azure Blockchain Workbench 一起提供。 使用 [Microsoft Q&A 问题页面](/answers/topics/azure-blockchain-workbench.html)获取支持。

## <a name="solution-architecture"></a>解决方案体系结构

使用 Ethereum 解决方案模板，可以部署基于单个或多个区域的多成员 Ethereum 权威证明联盟网络。

![部署体系结构](./media/ethereum-poa-deployment/deployment-architecture.png)

每个联盟成员部署包括：

* 用于运行 PoA 验证程序的虚拟机
* 用于分配 RPC、对等互连和 Governance DApp 请求的 Azure 负载均衡器
* 用于确保验证程序标识安全的 Azure Key Vault
* 用于托管持久性网络信息并协调租用的 Azure 存储
* 用于聚合日志和性能统计信息的 Azure Monitor
* VNet 网关（可选），允许跨专用 VNet 的 VPN 连接

默认情况下，可以通过公共 IP 访问 RPC 和对等互连终结点，以简化订阅和云之间的连接。 对于应用程序级访问控制，可以使用 [Parity 的权限协定](https://openethereum.github.io/Permissioning.html)。 支持在 VPN 后方部署网络，利用 VNet 网关实现跨订阅连接。 由于 VPN 和 VNet 部署更复杂，因此在进行解决方案原型设计时，你可能希望从公共 IP 模型开始。

Docker 容器用于可靠性和模块性。 Azure 容器注册表用于托管每个部署中包含的版本控制映像，并为其提供服务。 容器映像包括：

* Orchestrator - 生成标识和治理合同。 在标识存储区中存储标识。
* Parity 客户端 - 从身份存储库中租赁身份。 发现并连接到对等机。
* EthStats 代理 - 通过 RPC 收集本地日志和统计信息并将信息推送到 Azure Monitor。
* Governance DApp - 用于与治理合同进行交互的 Web 界面。

### <a name="validator-nodes"></a>验证程序节点

在权威证明协议中，验证程序节点取代了传统的挖矿程序节点。 每个验证程序都具有唯一的 Ethereum 标识，允许其参与块创建过程。 每个联盟成员可以跨五个区域预配两个或更多验证程序节点，用于提供异地冗余。 验证程序节点与其他验证程序节点进行通信以就基础分布式账本的状态达成共识。 为了确保公平参与网络，禁止每个联盟成员使用比网络上第一个成员更多的验证程序。 例如，如果第一个成员部署三个验证程序，则每个成员最多只能有三个验证程序。

### <a name="identity-store"></a>标识存储区

将在每个成员的订阅中部署标识存储，以安全地保存生成的 Ethereum 标识。 对于每个验证程序，编排容器将生成一个 Ethereum 私钥，并将其存储在 Azure Key Vault 中。

## <a name="deploy-ethereum-consortium-network"></a>部署 Ethereum 联盟网络

在本演练中，假设你要创建一个多方 Ethereum 联盟网络。 以下流是多方部署的一个示例：

1. 三个成员分别使用 MetaMask 生成 Ethereum 帐户
1. 成员 A 部署 Ethereum PoA，提供 Ethereum 公用地址
1. 成员 A 向成员 B 和成员 C 提供联盟 URL
1. 成员 B 和成员 C 部署 Ethereum PoA，提供其 Ethereum 公用地址和成员 A 的联盟 URL
1. 成员 A 投票赞成成员 B 作为管理员
1. 成员 A 和成员 B 都投票赞成成员 C 作为管理员

接下来的几部分将介绍如何在网络中配置第一个成员的占用量。

### <a name="create-resource"></a>创建资源

在 [Azure 门户](https://portal.azure.com)中，选择左上角的“创建资源”。

选择“区块链  >  Ethereum 权威证明联盟（预览版）”。 

### <a name="basics"></a>基础

在“基本信息”下面，指定适用于任何部署的标准参数的值。

![基础](./media/ethereum-poa-deployment/basic-blade.png)

参数 | 说明 | 示例值
----------|-------------|--------------
新建网络还是加入现有网络 | 你可以创建新的联盟网络，也可以加入现有的联盟网络。 需要其他参数才能加入现有网络。 | 新建
电子邮件地址 | 部署完成后，可收到包含相关部署信息的电子邮件通知。 | 有效的电子邮件地址
VM 用户名 | 部署的每个 VM 的管理员用户名 | 1 到 64 个字母数字字符
身份验证类型 | 对虚拟机进行身份验证的方法。 | 密码
密码 | 部署的每个虚拟机的管理员帐户密码。 最初，所有 VM 均具有相同的密码。 预配后便可以更改密码。 | 12-72 个字符 
订阅 | 部署联盟网络的订阅 |
资源组| 部署联盟网络的资源组。 | myResourceGroup
位置 | 资源组的 Azure 区域。 | 美国西部 2

选择“确定”。

### <a name="deployment-regions"></a>部署区域

在“部署区域”下，为每个区域指定区域和位置的数量。 最多可在五个区域中进行部署。 第一个区域应与“基本”部分中的资源组位置匹配。 对于开发或测试网络，你可以对每个成员使用一个区域。 对于生产用途，请跨两个或多个区域部署以实现高可用性。

![部署区域](./media/ethereum-poa-deployment/deployment-regions.png)

参数 | 说明 | 示例值
----------|-------------|--------------
区域数量|部署联盟网络的区域数量| 2
第一个区域 | 部署联盟网络的第一个区域 | 美国西部 2
第二个区域 | 部署联盟网络的第二个区域。 当区域数为 2 或更大时，其他区域可见。 | 美国东部 2

选择“确定”。

### <a name="network-size-and-performance"></a>网络规模和性能

在“网络规模和性能”下指定联盟网络规模的输入值。 验证程序节点存储大小可决定区块链的潜在大小。 可在部署后更改该大小。

![网络规模和性能](./media/ethereum-poa-deployment/network-size-and-performance.png)

参数 | 说明 | 示例值
----------|-------------|--------------
负载均衡验证程序节点数 | 要预配为网络组成部分的验证程序节点数。 | 2
验证程序节点存储性能 | 每个已部署验证程序节点的托管磁盘的类型。 有关定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/managed-disks/) | 标准 SSD
验证程序节点虚拟机大小 | 用于验证程序节点的虚拟机大小。 有关定价的详细信息，请参见[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | 标准 D2 v3

虚拟机和存储层将影响网络性能。  使用下表来帮助选择成本效率：

虚拟机 SKU|存储层|价格|吞吐量|延迟
---|---|---|---|---
F1|标准 SSD|low|low|high
D2_v3|标准 SSD|中|中|中
F16s|高级·SSD|high|high|low

选择“确定”。

### <a name="ethereum-settings"></a>Ethereum 设置

在“Ethereum 设置”下，指定 Ethereum 相关配置设置。

![Ethereum 设置](./media/ethereum-poa-deployment/ethereum-settings.png)

参数 | 说明 | 示例值
----------|-------------|--------------
联盟成员 ID | 与参与联盟网络的每个成员关联的 ID。 它用于配置 IP 地址空间以避免冲突。 对于专用网络，成员 ID 在同一网络中的不同组织之间应该是唯一的。  即使同一个组织部署到多个区域，也需要一个唯一的成员 ID。 记下此参数的值，因为需要与其他加入成员共享它，以确保不会产生冲突。 有效范围是 0 到 255。 | 0
网络 ID | 要部署的 Ethereum 联盟网络的网络 ID。 每个 Ethereum 网络都有自己的网络 ID，其中 1 是公共网络的 ID。 有效范围是 5 到 999,999,999 | 10101010
管理员 Ethereum 地址 | 用于参与 PoA 治理的 Ethereum 帐户地址。 可以使用 MetaMask 生成 Ethereum 地址。 |
高级选项 | 用于 Ethereum 设置的高级选项 | 启用
使用公共 IP 进行部署 | 如果选择专用 VNet，则网络将部署在 VNet 网关后面，并删除对等访问。 对于专用 VNet，则所有成员都必须使用 VNet 网关进行连接，以确保兼容性。 | 公共 IP
区块燃料限制 | 网络的起始区块燃料限制。 | 50000000
区块重新封装时间段（秒） | 网络上没有事务时创建空区块的频率。 较高的频率将加快结束，但会增加存储成本。 | 15
事务权限协定 | 事务权限协定的字节码。 限制智能合同部署和 Ethereum 帐户许可列表的执行。 |

选择“确定”。

### <a name="monitoring"></a>监视

监视允许你为网络配置日志资源。 监视代理从网络收集并显示有用的指标和日志，从而提供快速检查网络运行状况或调试问题的功能。

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

参数 | 说明 | 示例值
----------|-------------|--------------
监视 | 启用监视的选项 | 启用
连接到现有 Azure Monitor 日志 | 用于创建新 Azure Monitor 日志实例或加入现有实例的选项 | 新建
位置 | 部署新实例的区域 | 美国东部
现有日志分析工作区 ID（连接到现有 Azure Monitor 日志 = 加入现有）|现有 Azure Monitor 日志实例的工作区 ID|NA
现有日志分析主键（连接到现有 Azure Monitor 日志 = 加入现有）|用于连接到现有 Azure Monitor 日志实例的主键|NA

选择“确定”。

### <a name="summary"></a>总结

单击“摘要”以查看指定的输入并运行基本的部署前验证。 在部署之前，你可以下载模板和参数。

选择“创建”以进行部署。

如果部署包括 VNet 网关，则可能需要 45 至 50 分钟时间进行部署。

## <a name="deployment-output"></a>部署输出

部署完成后，可以使用 Azure 门户访问必需的参数。

### <a name="confirmation-email"></a>确认电子邮件

如果提供电子邮件地址（[基础知识](#basics)部分），则会发送一封电子邮件，其中包含部署信息和本文档的链接。

![部署电子邮件](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>门户

部署成功完成并且预配所有资源后，能够查看资源组中的输出参数。

1. 返回到门户中的资源组。
1. 选择“概述 > 部署”。

    ![资源组概述](./media/ethereum-poa-deployment/resource-group-overview.png)

1. 选择“icrosoft-azure-blockchain.azure-blockchain-ether-...”部署。
1. 选择“输出”部分。

    ![部署输出](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>扩大联盟

要扩展联盟，首先必须连接物理网络。 如果在 VPN 后方部署，请参阅[连接 VNet 网关](#connecting-vnet-gateways)部分，配置新成员部署过程中的网络连接。 完成部署后，请使用 [Governance DApp](#governance-dapp) 成为网络管理员。

### <a name="new-member-deployment"></a>新成员部署

与加入的成员共享以下信息。 在部署后的电子邮件或门户部署输出中找到此信息。

* 联盟数据 URL
* 已部署的节点数
* VNet 网关资源 ID（若使用 VPN）

部署成员在使用以下指导来部署其网络状态时，应使用相同的 Ethereum 权威证明联盟解决方案模板：

* 选择“加入现有”
* 选择与网络上其他成员相同的验证程序节点数，以确保公平表示
* 使用相同的管理员 Ethereum 地址
* 使用“Ethereum 设置”中的“联盟数据 URL” 
* 如果网络的其余部分位于 VPN 后方，请在高级部分选择“专用 VNet”

### <a name="connecting-vnet-gateways"></a>连接 VNet 网关

仅当使用专用 VNet 部署时，才需要此部分。 如果使用公共 IP 地址，则可以跳过此部分。

对于专用网络，不同的成员通过 VNet 网关连接进行连接。 在成员可以加入网络并查看事务流量之前，现有成员必须在其 VPN 网关上进行最终配置才能接受此连接。 连接成员的 Ethereum 节点在建立连接前不会运行。 要减少单点故障的可能性，请在联盟中创建冗余网络连接。

部署新成员后，现有成员必须通过设置与新成员的 VNet 网关连接来完成双向连接。 现有成员需要：

* 连接成员的 VNet 网关 ResourceID。 请参阅[部署输出](#deployment-output)。
* 共享的连接密钥。

现有成员必须运行以下 PowerShell 脚本来完成连接。 你可以使用位于门户右上角导航栏中的 Azure Cloud Shell。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>Governance DApp

权威证明的核心是分散式治理。 由于权威证明依赖于网络授权机构许可列表来保持网络健康，因此提供一个公平的机制来修改此权限列表非常重要。 每个部署都带有一组智能合同和门户，用于对此许可列表进行链式治理。 一旦提议的更改获得联盟成员的多数票，就会执行更改。 投票允许以一种激励诚信网络的透明方式添加新的共识参与者或删除受到损害的参与者。

Governance DApp 是一组预部署的[智能合同](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts)和 Web 应用程序，用于治理网络上的授权机构。 授权机构分为管理员标识和验证程序节点。
管理员有权将共识参与委托给一组验证程序节点。 管理员还可以将投票给网络内外的其他管理员。

![Governance DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **分散式治理：** 网络授权机构的更改由选定的管理员通过链式投票进行管理。
* **验证程序委派：** 授权机构可以管理在每个 PoA 部署中设置的验证程序节点。
* **可审核的更改历史记录：** 每次更改都记录在区块链上，提供了透明度和可审核性。

### <a name="getting-started-with-governance"></a>Governance 入门

若要通过 Governance DApp 执行任何类型的事务，将使用 Ethereum 电子钱包。 最简单的方法是使用 [MetaMask](https://metamask.io) 等浏览器内电子钱包；但是，由于它们是网络上部署的智能合同，因此还可以自动化与 Governance 合同的交互。

安装 MetaMask 后，在浏览器中导航到 Governance DApp。  可以通过 Azure 门户在部署输出中找到 URL。  如果未安装浏览器内电子钱包，将无法执行任何操作；但可以查看管理员状态。

### <a name="becoming-an-admin"></a>成为管理员

如果你是网络上部署的第一个成员，则将自动成为管理员，并且奇偶校验节点将被列为验证程序。 如果你要加入网络，则将需要大多数（大于 50%）的现有管理员集投票赞成你作为管理员。 如果你选择不成为管理员，则节点仍将同步并验证区块链；但是，它们不会参与块创建过程。 若要启动投票过程以成为管理员，请选择“提名”并输入 Ethereum 地址和别名。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候选人

选择“候选人”选项卡将向你显示当前候选管理员集。  一旦候选人的票数超过当前管理员的票数，候选人就会被提升为管理员。要对候选人进行投票，请选择该行，然后选择“投票选出”。 如果要更改投票，请选择候选人，然后选择“撤销投票”。

![候选人](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理员

“管理员”选项卡将显示当前管理员集并允许你投反对票。  一旦管理员失去超过 50% 的支持，就会删除他们在网络上的管理员身份。 管理员拥有的任何验证程序节点将丢失验证程序状态并成为网络上的事务节点。 管理员被删除的原因可能有多种；但是，由联盟决定是否提前同意策略。

![管理员](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>验证程序

选择“验证程序”选项卡将显示实例的当前已部署的奇偶校验节点及其当前状态（节点类型）。 由于此视图表示当前已部署的联盟成员，因此每个联盟成员在此列表中会有不同的验证程序集。 如果实例是新部署的，并且尚未添加验证程序，则可以看到“添加验证程序”选项。 添加验证程序将自动选择一组区域平衡奇偶校验节点并将其分配给你的验证程序集。 如果已部署的节点数大于允许的容量，则剩余节点将成为网络上的事务节点。

每个验证程序的地址将通过 Azure 中的[标识存储区](#identity-store)自动分配。  如果某个节点出现故障，则将放弃其标识，允许部署中的其他节点来取代它。 这一过程可确保一致参与高度可用。

![验证程序](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>联盟名称

任何管理员都可以更新联盟名称。  选择左上角的齿轮图标可更新联盟名称。

### <a name="account-menu"></a>帐户菜单

右上角是 Ethereum 帐户别名和 identicon。  如果你是管理员，则可以更新你的别名。

![帐户](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>支持和反馈<a id="tutorials"></a>

对于 Azure 区块链新闻，请访问 [Azure 区块链博客](https://azure.microsoft.com/blog/topics/blockchain/)，以随时了解 Azure 区块链工程团队提供的区块链服务产品和信息。

若要提供产品反馈或请求新功能，请通过[区块链的 Azure 反馈论坛](https://aka.ms/blockchainuservoice)来发布想法或进行投票。

### <a name="community-support"></a>社区支持

与 Microsoft 工程师和 Azure 区块链社区专家交流。

* [Microsoft Q&A 问题页](/answers/topics/azure-blockchain-workbench.html)。 针对区块链模板的工程支持仅限于部署问题。
* [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>后续步骤

有关更多 Azure 区块链解决方案，请参阅 [Azure 区块链文档](../index.yml)。
