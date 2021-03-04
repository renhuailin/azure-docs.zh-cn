---
title: 在 Azure 上部署以太坊证书颁发机构协会解决方案模板
description: 使用以太坊的证书颁发机构协会解决方案在 Azure 上部署和配置多成员协会以太坊网络
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097935"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>在 Azure 上部署以太坊证书颁发机构协会解决方案模板

你可以使用 [以太坊的证书颁发机构协会预览版 azure 解决方案模板](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) ，以最少的 Azure 和以太坊知识来部署、配置和控制多成员协会的授权以太坊网络。

每个联合会成员均可使用解决方案模板，通过 Microsoft Azure 计算、网络和存储服务来预配区块链网络需求量。 每个联合会成员的网络需求量包含一组负载均衡的验证程序节点，应用程序或用户可与这些节点进行交互，以提交以太坊事务。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>选择 Azure 区块链解决方案

在选择使用 "以太坊认证证书协会" 解决方案模板之前，请将方案与可用 Azure 区块链选项的常见用例进行比较。

> [!IMPORTANT]
> 请考虑使用 [Azure 区块链服务](../service/overview.md) ，而不是 azure 解决方案模板上的以太坊。 Azure 区块链服务是受支持的托管 Azure 服务。 奇偶校验以太坊过渡到社区驱动的开发和维护。 有关详细信息，请参阅 [将奇偶校验以太坊转换为 OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/)。

选项 | 服务模型 | 常见用例
-------|---------------|-----------------
解决方案模板 | IaaS | 解决方案模板是 Azure 资源管理器模板，可用于预配完全配置的区块链网络拓扑。 模板为给定区块链网络类型部署和配置 Microsoft Azure 计算、网络和存储服务。 提供解决方案模板时没有服务级别协议。 使用 [Microsoft Q&A 问题页面](/answers/topics/azure-blockchain-workbench.html)获取支持。
[Azure 区块链服务](../service/overview.md) | PaaS | Azure 区块链服务预览版简化了联合会区块链网络的构成、管理和监管。 将 Azure 区块链服务用于需要 PaaS、联合会管理或合同和事务隐私的解决方案。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 预览版是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 使用 Azure 区块链工作台为区块链解决方案或区块链应用程序概念证明。 服务级别协议未随 Azure Blockchain Workbench 一起提供。 使用 [Microsoft Q&A 问题页面](/answers/topics/azure-blockchain-workbench.html)获取支持。

## <a name="solution-architecture"></a>解决方案体系结构

使用以太坊解决方案模板，可以部署基于单个或多个区域的多成员以太坊协会网络。

![部署体系结构](./media/ethereum-poa-deployment/deployment-architecture.png)

每个联盟成员部署包括：

* 用于运行 PoA 验证程序的虚拟机
* 用于分发 RPC、对等互连和调控 DApp 请求的 Azure 负载均衡器
* 用于确保验证程序标识安全的 Azure Key Vault
* 用于托管持久性网络信息并协调租用的 Azure 存储
* 用于聚合日志和性能统计信息的 Azure Monitor
* VNet 网关（可选），允许跨专用 VNet 的 VPN 连接

默认情况下，可以通过公共 IP 访问 RPC 和对等互连终结点，以简化订阅和云之间的连接。 对于应用程序级访问控制，可以使用 [奇偶校验的权限协定](https://openethereum.github.io/Permissioning.html)。 支持在 Vpn 后面部署的网络，这些网络利用 VNet 网关实现跨订阅连接。 由于 VPN 和 VNet 部署更复杂，因此在原型设计解决方案时，您可能希望从公共 IP 模型开始。

Docker 容器用于可靠性和模块化。 Azure 容器注册表用于作为每个部署的一部分承载和提供版本控制映像。 容器映像包括：

* Orchestrator-生成标识和调控协定。 在标识存储中存储标识。
* 身份验证客户端-从标识存储中借用标识。 发现并连接到对等方。
* EthStats Agent-通过 RPC 收集本地日志和统计信息，并将信息推送到 Azure Monitor。
* 调控 DApp-用于与调控协定交互的 Web 界面。

### <a name="validator-nodes"></a>验证器节点

在权威证明协议中，验证程序节点取代了传统的挖矿程序节点。 每个验证程序都具有唯一的以太坊标识，允许其参与块创建过程。 每个联盟成员可以跨五个区域预配两个或更多验证程序节点，用于提供异地冗余。 验证程序节点与其他验证程序节点进行通信以就基础分布式账本的状态达成共识。 为了确保公平参与网络，禁止每个联合会成员使用比网络上第一个成员更多的验证程序。 例如，如果第一个成员部署三个验证程序，则每个成员最多只能有三个验证程序。

### <a name="identity-store"></a>标识存储区

将在每个成员的订阅中部署标识存储，以安全地保存生成的以太坊标识。 对于每个验证程序，orchestration 容器将生成一个以太坊私钥，并将其存储在 Azure Key Vault 中。

## <a name="deploy-ethereum-consortium-network"></a>部署以太坊联合会网络

在本演练中，假设你要创建一个多方以太坊联合会网络。 以下流是多方部署的一个示例：

1. 三个成员分别使用 MetaMask 生成 Ethereum 帐户
1. *成员 A* 部署以太坊 PoA，提供其以太坊公用地址
1. 成员 A 向成员 B 和成员 C 提供联盟 URL
1. 成员 B 和成员 C 部署 Ethereum PoA，提供其 Ethereum 公用地址和成员 A 的联盟 URL
1. 成员 A 投票赞成成员 B 作为管理员
1. 成员 A 和成员 B 都投票赞成成员 C 作为管理员

接下来的部分介绍如何在网络中配置第一个成员的占用量。

### <a name="create-resource"></a>创建资源

在 [Azure 门户](https://portal.azure.com)中，选择左上角的 " **创建资源** "。

选择 **区块链**  >  **以太坊 (preview)**。

### <a name="basics"></a>基本知识

在 " **基本** 信息" 下，指定任何部署的标准参数的值。

![基本知识](./media/ethereum-poa-deployment/basic-blade.png)

参数 | 说明 | 示例值
----------|-------------|--------------
创建新网络或加入现有网络 | 你可以创建新的联合会网络，或加入预先存在的联合会网络。 加入现有网络需要其他参数。 | 新建
电子邮件地址 | 部署完成时，将收到电子邮件通知，其中包含有关部署的信息。 | 有效的电子邮件地址
VM 用户名 | 部署的每个 VM 的管理员用户名 | 1-64 字母数字字符
身份验证类型 | 对虚拟机进行身份验证的方法。 | 密码
密码 | 部署的每个虚拟机的管理员帐户密码。 所有 Vm 最初都具有相同的密码。 你可以在设置后更改密码。 | 12-72 个字符 
订阅 | 部署联盟网络的订阅 |
资源组| 部署联盟网络的资源组。 | myResourceGroup
位置 | 资源组的 Azure 区域。 | 美国西部 2

选择“确定”。

### <a name="deployment-regions"></a>部署区域

在 " *部署区域*" 下，指定每个区域和位置的数量。 最多可部署五个区域。 第一个区域应与 " *基本* 信息" 部分中的 "资源组位置" 匹配。 对于开发或测试网络，可以对每个成员使用单个区域。 对于生产环境，请跨两个或多个区域进行部署以实现高可用性。

![部署区域](./media/ethereum-poa-deployment/deployment-regions.png)

参数 | 说明 | 示例值
----------|-------------|--------------
区域数量|部署联盟网络的区域数量| 2
第一个区域 | 部署联盟网络的第一个区域 | 美国西部 2
第二个区域 | 用于部署联合会网络的第二个区域。 当区域数为2或更大时，其他区域可见。 | 美国东部 2

选择“确定”。

### <a name="network-size-and-performance"></a>网络规模和性能

在 " *网络大小和性能*" 下，指定联合会网络大小的输入。 验证器节点存储大小指示区块链的潜在大小。 部署后可以更改大小。

![网络规模和性能](./media/ethereum-poa-deployment/network-size-and-performance.png)

参数 | 说明 | 示例值
----------|-------------|--------------
负载均衡验证程序节点数 | 要作为网络的一部分预配的验证程序节点数。 | 2
验证程序节点存储性能 | 每个已部署的验证程序节点的托管磁盘的类型。 有关定价的详细信息，请参阅 [存储定价](https://azure.microsoft.com/pricing/details/managed-disks/) | 标准 SSD
验证程序节点虚拟机大小 | 用于验证程序节点的虚拟机大小。 有关定价的详细信息，请参阅 [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | 标准 D2 v3

虚拟机和存储层会影响网络性能。  使用下表来帮助选择成本效率：

虚拟机 SKU|存储层|价格|吞吐量|延迟
---|---|---|---|---
F1|标准 SSD|low|low|high
D2_v3|标准 SSD|中|中|中
F16s|高级·SSD|high|high|low

选择“确定”。

### <a name="ethereum-settings"></a>Ethereum 设置

在 " *以太坊设置*" 下，指定与以太坊相关的配置设置。

![Ethereum 设置](./media/ethereum-poa-deployment/ethereum-settings.png)

参数 | 说明 | 示例值
----------|-------------|--------------
联盟成员 ID | 与参与联盟网络的每个成员关联的 ID。 它用于配置 IP 地址空间以避免冲突。 对于专用网络，同一网络中的不同组织成员 ID 应该是唯一的。  即使同一个组织部署到多个区域，也需要一个唯一的成员 ID。 请记下此参数的值，因为需要与其他联接成员共享它，以确保不会发生冲突。 有效范围为0至255。 | 0
网络 ID | 要部署的 Ethereum 联盟网络的网络 ID。 每个 Ethereum 网络都有自己的网络 ID，其中 1 是公共网络的 ID。 有效范围为5到999999999 | 10101010
管理员 Ethereum 地址 | 用于参与 PoA 管理的以太坊帐户地址。 可以使用 MetaMask 生成以太坊地址。 |
高级选项 | 用于 Ethereum 设置的高级选项 | 启用
使用公共 IP 进行部署 | 如果选择了 "专用 VNet"，则会将网络部署在 VNet 网关之后，并删除对等互连访问。 对于专用 VNet，所有成员必须使用 VNet 网关才能使连接兼容。 | 公共 IP
阻止气体限制 | 网络的起始块加油站限制。 | 50000000
区块重新封装时间段（秒） | 网络上没有事务时创建空区块的频率。 较高的频率将加快结束，但会增加存储成本。 | 15
事务权限协定 | 事务权限协定的字节码。 将智能协定部署和执行限制到以太坊帐户的允许列表。 |

选择“确定”。

### <a name="monitoring"></a>监视

监视允许您为网络配置日志资源。 监视代理从你的网络收集和显示有用的指标和日志，提供快速查看网络运行状况或调试问题的功能。

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

参数 | 说明 | 示例值
----------|-------------|--------------
监视 | 启用监视的选项 | 启用
连接到现有 Azure Monitor 日志 | 用于创建新 Azure Monitor 日志实例或加入现有实例的选项 | 新建
位置 | 部署新实例的区域 | 美国东部
 (连接到现有 Azure Monitor 日志的现有 log analytics 工作区 ID = 加入现有) |现有 Azure Monitor 日志实例的工作区 ID||NA
现有的 log analytics 主密钥 (连接到现有 Azure Monitor 日志 = 加入现有) |用于连接到现有 Azure Monitor 日志实例的主键||NA

选择“确定”。

### <a name="summary"></a>总结

单击摘要以查看指定的输入，并运行基本的部署前验证。 在部署之前，您可以下载模板和参数。

选择“创建”以进行部署。

如果部署包括 VNet 网关，部署可能需要45到50分钟。

## <a name="deployment-output"></a>部署输出

部署完成后，可以使用 Azure 门户访问必需的参数。

### <a name="confirmation-email"></a>确认电子邮件

如果提供了电子邮件地址 ([基础知识 "部分](#basics)) ，则会发送一封电子邮件，其中包含部署信息以及本文档的链接。

![部署电子邮件](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>门户

成功完成部署并设置所有资源后，可以查看资源组中的输出参数。

1. 在门户中转到资源组。
1. 选择 " **概述 > 部署**"。

    ![资源组概述](./media/ethereum-poa-deployment/resource-group-overview.png)

1. 选择 " **区块链** "，然后-... 部署。
1. 选择 " **输出** " 部分。

    ![部署输出](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>扩大联盟

要扩展联盟，首先必须连接物理网络。 如果在 VPN 后面进行部署，请参阅 [连接 VNet 网关](#connecting-vnet-gateways) 部分，将网络连接配置为新成员部署的一部分。 部署完成后，请使用 [管理 DApp](#governance-dapp) 来成为网络管理员。

### <a name="new-member-deployment"></a>新成员部署

与加入的成员共享以下信息。 此信息可在部署后电子邮件或门户部署输出中找到。

* 联盟数据 URL
* 部署的节点数
* VNet 网关资源 ID（若使用 VPN）

部署成员的网络存在时，部署成员应使用相同的 "以太坊" 证书颁发机构协会解决方案模板：

* 选择“加入现有”
* 选择与网络上的其他成员数量相同的验证程序节点，以确保公平表示
* 使用相同的管理员以太坊地址
* 在 *以太坊设置* 中使用提供的 *联盟数据 Url*
* 如果网络的其余部分位于 VPN 后面，请在 "高级" 部分下选择 " **专用 VNet** "

### <a name="connecting-vnet-gateways"></a>连接 VNet 网关

仅当使用专用 VNet 部署时，才需要此部分。 如果使用公共 IP 地址，则可以跳过此部分。

对于专用网络，不同成员通过 VNet 网关连接进行连接。 在成员可以加入网络并查看事务流量之前，现有成员必须在其 VPN 网关上执行最终配置，以接受连接。 在建立连接之前，联接成员的以太坊节点将不会运行。 若要减少单点故障的可能性，请在协会中创建冗余网络连接。

部署新成员后，现有成员必须通过设置与新成员的 VNet 网关连接来完成双向连接。 现有成员需要：

* 连接成员的 VNet 网关 ResourceID。 请参阅 [部署输出](#deployment-output)。
* 共享连接键。

现有成员必须运行以下 PowerShell 脚本来完成连接。 可以使用门户中右上方导航栏中的 Azure Cloud Shell。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
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

权威证明的核心是分散式治理。 由于证书颁发机构依赖于允许的网络颁发机构列表来使网络保持正常运行，因此提供一种公平机制来对此权限列表进行修改非常重要。 每个部署都附带一组智能协定和门户，适用于此允许列表的链间管理。 一旦提议的更改获得联盟成员的多数票，就会执行更改。 投票允许将新的共识参与者添加到或被侵害，以一种可鼓励诚实网络的透明方式删除参与者。

调控 DApp 是一套预先部署的 [智能协定](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) 和一个用于控制网络上的权限的 web 应用程序。 颁发机构分为管理标识和验证器节点。
管理员可以将共识参与委托给一组验证器节点。 管理员还可以将投票给网络内外的其他管理员。

![Governance DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **分散管理：** 网络颁发机构的更改是通过 "选择管理员" 通过链投票进行管理。
* **验证程序委托：** 颁发机构可以管理在每个 PoA 部署中设置的验证程序节点。
* 可 **审核的更改历史记录：** 每个更改都记录在区块链上，提供透明度和可审核性。

### <a name="getting-started-with-governance"></a>Governance 入门

若要通过管理 DApp 执行任何类型的事务，需要使用以太坊钱包。 最简单的方法是使用 [MetaMask](https://metamask.io)中的浏览器钱包;但是，由于这些智能协定部署在网络上，因此你还可以自动执行与调控协定的交互。

安装 MetaMask 后，在浏览器中导航到 Governance DApp。  您可以通过在部署输出中 Azure 门户找到该 URL。  如果未安装浏览器内钱包，将无法执行任何操作;但是，您可以查看管理员状态。  

### <a name="becoming-an-admin"></a>成为管理员

如果你是在网络上部署的第一个成员，则你将自动成为管理员，而你的奇偶校验节点作为验证程序列出。 如果加入网络，则需要在现有管理员集的大部分 (大于 50% ) 的情况下，以管理员身份投票。 如果选择不成为管理员，则节点仍将同步并验证区块链;但是，它们并不参与创建块。 若要启动投票过程以成为管理员，请选择 "以太坊" 并输入你的 **地址和别名** 。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候选人

选择 " **候选** " 选项卡将显示当前候选管理员组。  当候选项达到当前管理员的大多数投票后，候选项会提升为管理员。 若要对候选项投票，请选择该行，然后选择 " **在中投票**"。 如果你在投票上改变主意，请选择候选项，然后选择 " **Rescind 投票**"。

![候选人](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理员

" **管理员** " 选项卡显示当前管理员集，并提供对的投票功能。  管理员丢失超过50% 的支持后，将作为网络上的管理员删除。 管理员拥有的任何验证器节点都将丢失验证程序状态，并成为网络上的事务节点。 可能出于多种原因删除管理员;不过，它会提前对策略达成一致。

![管理员](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>验证程序

选择 " **验证** 程序" 选项卡将显示实例的当前已部署的奇偶校验节点及其当前状态 (节点类型) 。 每个联合会成员在此列表中都有一组不同的验证程序，因为此视图表示当前已部署的联合会成员。 如果实例是新部署的，并且尚未添加验证程序，则可以选择 **添加验证** 程序。 添加验证器会自动选择突破均衡的奇偶校验集，并将它们分配给验证器集。 如果部署的节点数量超过了允许的容量，则其余节点将成为网络上的事务节点。

每个验证程序的地址将通过 Azure 中的[标识存储区](#identity-store)自动分配。  如果某个节点出现故障，它将让给其标识，允许部署中的另一个节点执行该节点。 此过程可确保你的共识参与高度可用。

![验证程序](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>联盟名称

任何管理员都可以更新联盟名称。  选择左上角的齿轮图标以更新联盟名称。

### <a name="account-menu"></a>帐户菜单

右上方为你的以太坊帐户别名和 identicon。  如果你是管理员，则可以更新别名。

![帐户](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>支持和反馈<a id="tutorials"></a>

对于 Azure 区块链新闻，请访问 [Azure 区块链博客](https://azure.microsoft.com/blog/topics/blockchain/)，以随时了解 Azure 区块链工程团队提供的区块链服务产品和信息。

若要提供产品反馈或请求新功能，请通过[区块链的 Azure 反馈论坛](https://aka.ms/blockchainuservoice)来发布想法或进行投票。

### <a name="community-support"></a>社区支持

与 Microsoft 工程师和 Azure 区块链社区专家交流。

* [Microsoft Q&问题页面](/answers/topics/azure-blockchain-workbench.html)。 针对区块链模板的工程支持仅限于部署问题。
* [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>后续步骤

有关更多 Azure 区块链解决方案，请参阅 [Azure 区块链文档](../index.yml)。
