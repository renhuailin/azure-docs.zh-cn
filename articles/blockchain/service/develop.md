---
title: Azure 区块链服务开发概述
description: 介绍如何在 Azure 区块链服务上开发解决方案。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348378"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure 区块链服务开发概述

使用 Azure 区块链服务，可以创建联盟区块链网络，以实现企业方案，如资产跟踪、数字令牌、会员和奖励、供应链金融和源头。 以下部分介绍了用于实现企业区块链解决方案的 Azure 区块链服务开发。

## <a name="connecting-to-azure-blockchain-service"></a>连接到 Azure 区块链服务

区块链网络有不同类型的客户端，包括全节点、轻节点和远程客户端。 Azure 区块链服务构建包含节点的区块链网络。 可以使用不同的客户端作为 Azure 区块链服务的网关进行区块链开发。 Azure 区块链服务提供基本身份验证或访问密钥作为开发终结点。 下面是一些可以使用连接的常用客户端。

### <a name="visual-studio-code"></a>Visual Studio Code

可以使用 Azure 区块链开发工具包 Visual Studio Code 扩展连接到联盟成员。 连接到联盟后，可以编译、生成智能合同并将其部署到 Azure 区块链服务联盟成员。

若要开发复杂的企业区块链解决方案，需要使用开发框架连接到不同的区块链网络，并管理智能合同生命周期。 大多数项目与至少两个区块链节点进行交互。 开发人员在开发期间使用本地区块链。 当应用程序准备好进行测试或发布时，开发人员将其部署到区块链网络。 例如，主公用 Ethereum 网络或 Azure 区块链服务。 Visual Studio Code 中适用于 Ethereum 的 Azure 区块链开发工具包扩展使用 Truffle。 Truffle 是一种常用的区块链开发框架，用于在 Ethereum 区块链上写入、编译、部署和测试分散的应用程序。 还可以将 Truffle 看作是一个尝试无缝集成智能合同开发和传统 web 开发的框架。

有关详细信息，请参阅[快速入门：使用 Visual Studio Code 连接到 Azure 区块链服务联盟网络快速入门](connect-vscode.md)。

### <a name="metamask"></a>MetaMask

MetaMask 是基于浏览器的钱包（远程客户端）、RPC 客户端和基本合同资源管理器。 与其他浏览器钱包不同，MetaMask 将 web3 实例注入浏览器 JavaScript 上下文，用作连接到各种 Ethereum 区块链（“mainnet”、“Ropsten testnet”、“Kovan testnet”、本地 RPC 节点）的 RPC 客户端。   可以轻松地将自定义 RPC 设置为连接到 Azure 区块链服务，并使用 Remix 开始区块链开发。

有关详细信息，请参阅[快速入门：使用 MetaMask 进行连接并部署智能合同](connect-metamask.md)

### <a name="geth"></a>Geth

Geth 是命令行接口，用于运行在 Go 中实现的全 Ethereum 节点。 无需运行全节点，但可以启动其交互式控制台，它提供了一个公开 JavaScript API 的 JavaScript 运行时环境来与 Azure 区块链服务进行交互。

有关详细信息，请参阅[快速入门：使用 Geth 附加到 Azure 区块链服务事务节点](connect-geth.md)。

## <a name="ethereum-quorum-private-transactions"></a>Ethereum Quorum 专用事务

Quorum 是一个基于 Ethereum 的分布式账本协议，其中包含事务和合同隐私以及新的共识机制。 Go-Ethereum 上的主要增强功能包括：

* “隐私” - Quorum 通过公用和专用状态分离来支持专用事务和专用合同，并利用对等加密消息交换，将专用数据定向传送到网络参与者。
* “备用共识机制” - 经授权网络不需要工作证明或权益证明共识。 Quorum 提供多种共识机制，设计用于 RAFT 和 IBFT 等联盟链。  Azure 区块链服务使用 IBFT 共识机制。
* “对等授权” - 使用智能合同的节点和对等授权确保只有已知的参与方可以加入网络。
* “更高的性能” - Quorum 比公用 Geth 提供更高的性能。

## <a name="block-explorers"></a>块资源管理器

块资源管理器是联机区块链浏览器，用于显示单个块内容、事务地址数据和历史记录。 可以通过 Azure 区块链服务中的 Azure Monitor 获取基本的块信息。 但是，如果在开发过程中需要更多详细信息，块资源管理器会很有用。  以下块资源管理器使用 Azure 区块链服务：

* Web3 实验室的[Epirus Azure 区块链服务资源管理器](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

还可以使用区块链数据管理器和 Azure Cosmos DB 来生成自己的块资源管理器，请参阅[教程：使用区块链数据管理器向 Azure Cosmos DB 发送数据](data-manager-cosmosdb.md)。

## <a name="tps-measurement"></a>TPS 度量

当区块链用于更多企业方案时，每秒事务数 (TPS) 速度对于避免瓶颈和系统效率低下很重要。 在分散的区块链中很难维持高事务速率。 精确的 TPS 度量可能受不同因素的影响，如服务器线程、事务队列大小、网络延迟和安全性。 如果在开发过程中需要测量 TPS 速度，常用的开放源代码工具是 [ChainHammer](https://github.com/drandreaskrueger/chainhammer)。

## <a name="next-steps"></a>后续步骤

请尝试学习快速入门，使用适用于 Ethereum 的 Azure 区块链开发工具包将联盟附加到 Azure 区块链服务。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)