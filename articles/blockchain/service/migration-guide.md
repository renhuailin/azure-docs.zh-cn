---
title: Azure 区块链服务停用通知和指南
description: 将 Azure 区块链服务迁移到托管或自托管的区块链产品/服务
ms.date: 05/10/2021
ms.topic: how-to
ms.openlocfilehash: 6a767a8e4d31cd76012039f40e688f08708fc0c2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541542"
---
# <a name="migrate-azure-blockchain-service"></a>迁移 Azure 区块链服务

可以将账本数据从 Azure 区块链服务迁移到备选产品/服务。

> [!IMPORTANT]
> Azure 区块链将于 2021 年 9 月 10 日停用。 请根据生产或评估中的开发状态，将账本数据从 Azure 区块链服务迁移到备选产品/服务。

## <a name="evaluate-alternatives"></a>评估备选项

规划迁移的第一步是评估备选产品/服务。 根据正在生产或评估的开发状态，评估以下备选方案。

### <a name="production-or-pilot-phase"></a>生产或试点阶段

如果已部署并开发了处于生产或试点阶段中的区块链解决方案，请考虑以下备选方案。

#### <a name="quorum-blockchain-service"></a>Quorum 区块链服务

Quorum 区块链服务是 ConsenSys 在 Azure 上提供的托管产品/服务，支持 Quorum 作为账本技术。

- **托管产品/服务** - 与 Azure 区块链服务相比，Quorum 区块链服务没有额外的管理开销。
- **账本技术** - 基于 ConsenSys Quorum，它是 Azure 区块链服务中使用的 GoQuorum 账本技术的增强版本。 无需学习任何新知识。 有关详细信息，请参阅 [Consensys Quorum 常见问题解答](https://consensys.net/quorum/faq)。
- **连续性** - 可以通过 ConsenSys 将现有数据迁移到 Quorum 区块链服务。 有关详细信息，请参阅[从 Azure 区块链服务导出数据](#export-data-from-azure-blockchain-service)

有关详细信息，请参阅 [Quorum 区块链服务](https://consensys.net/QBS)。

#### <a name="azure-vm-based-deployment"></a>基于 Azure VM 的部署

有几种区块链资源管理模板可以用于在 IaaS VM 上部署区块链。

- **账本技术** - 可以继续使用 Quorum 账本技术，包括新的 ConsenSys Quorum。
- **自我管理** - 部署后，可以管理基础结构和区块链堆栈。

### <a name="new-deployment-or-evaluation-phase"></a>新的部署或评估阶段

如果开始开发新解决方案或处于评估阶段，请根据场景需求考虑以下备用方案。

- [Azure 市场中的 Quorum 模板](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.quorum-dev-quickstart)
- Azure 市场中的 Besu 模板

### <a name="how-to-migrate-to-an-alternative"></a>如何迁移到备用方案

若要迁移生产工作负载，请首先[从 Azure 区块链服务导出数据](#export-data-from-azure-blockchain-service)。 获取数据副本后，可以将此数据转换为首选备用方案。

建议的迁移目标为 ConsenSys Quorum 区块链服务。 若要加入此服务，请在 [Quorum 区块链服务](https://consensys.net/QBS)页注册。

要使用 Azure 中的虚拟机自行管理区块链解决方案，请参阅[基于 Azure VM 的 Quorum 指南](#azure-vm-based-quorum-guidance)以设置事务和验证器节点。
## <a name="export-data-from-azure-blockchain-service"></a>从 Azure 区块链服务中导出数据

根据当前的开发状态，你可以选择使用 Azure 区块链服务上的现有账本数据，或者启动一个新网络并使用所选择的解决方案。 建议在所有不需要或不打算使用 Azure 区块链服务上的现有账本数据的情况下，基于自己选择的解决方案创建一个新的联盟。

### <a name="open-support-case"></a>提交支持案例

如果有付费支持计划，请创建 Microsoft 支持工单以暂停联盟并导出区块链数据。

1. 使用 Azure 门户创建支持工单。 在“问题说明”中，输入以下详细信息：

    ![Azure 门户中的支持工单问题描述窗体](./media/migration-guide/problem-description.png)

    | 字段 | 响应 |
    |-------|--------- |
    | 问题类型 | 技术 |
    | 服务 | Azure 区块链服务 - 预览版 |
    | 总结 | 请求数据进行迁移 |
    | 问题类型 | 其他 |

1. 在“其他详细信息”中，请包括以下详细信息：

    ![Azure 门户中的支持工单其他详细信息窗体](./media/migration-guide/additional-details.png)

    - 订阅 ID 或 Azure 资源管理器资源 ID
    - 租户
    - 联盟名称
    - 区域
    - 成员名称
    - 启动迁移的首选日期时间

如果你的联盟有多个成员，则每个成员都需要为各自的成员数据创建单独的支持工单。

### <a name="pause-consortium"></a>暂停联盟

你需要协调联盟成员进行数据导出，因为联盟将因数据导出而暂停，并且在此期间的事务将失败。

Azure 区块链服务团队将暂停联盟，导出数据快照，并使数据可以通过生存期较短的 SAS URL 以加密格式下载。 在拍摄快照后，会恢复联盟。

> [!IMPORTANT]
> 应停止向网络发起新区块链事务的所有应用程序。 活动应用程序可能会导致数据丢失或原始网络和迁移的网络不同步。

### <a name="download-data"></a>下载数据

#### <a name="data-format-v1"></a>数据格式 v1

使用 Microsoft 支持部门提供的生存期较短的 SAS URL 链接下载数据。

> [!IMPORTANT]
> 你需要在七天内下载数据。

使用 API 访问密钥对数据进行解密。 可以[从 Azure 门户](configure-transaction-nodes.md#access-keys)或[通过 REST API](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) 获取密钥。

> [!CAUTION]
> 只有默认的事务节点 API 访问密钥 1 可用于加密该成员的所有节点数据。
>
> 请勿在迁移期间重置 API 访问密钥。

#### <a name="data-format-v2"></a>数据格式 v2

在此版本中，对 SAS 令牌（而不是数据）进行加密，从而更快地创建快照。 如果选择迁移到 ConsenSys Quorum 区块链服务，则导入到 Quorum 区块链服务的速度也会更快。

对 SAS 令牌进行解密后，可以照常下载数据。 数据本身没有额外的加密层。

> [!IMPORTANT]
> 创建数据格式 v2 快照的速度大约快 8 - 10 倍，因此停机时间更少。

> [!CAUTION]
> 默认事务节点 API 访问密钥 1 用于加密 SAS 令牌。
>
> 请勿在迁移之间或迁移期间重置 API 访问密钥。

可将数据用于 ConsenSys Quorum 区块链服务或基于 IaaS VM 的部署。

对于 ConsenSys Quorum 区块链服务迁移，请通过 [qbsmigration@consensys.net](mailto:qbsmigration@consensys.net) 联系 ConsenSys。

要将数据用于基于 IaaS VM 的部署，请按照本文[基于 Azure VM 的 Quorum 指南](#azure-vm-based-quorum-guidance)部分中的步骤进行操作。

### <a name="delete-resources"></a>删除资源

完成数据复制后，建议删除 Azure 区块链成员资源。 这些资源存在的话将继续向你收费。

## <a name="azure-vm-based-quorum-guidance"></a>基于 Azure VM 的 Quorum 指南

使用以下步骤创建事务节点和验证器节点。

### <a name="transaction-node"></a>事务节点

事务节点有两个组件。 Tessera 用于专用事务，Geth 用于 Quorum 应用程序。 验证器节点只需要 Geth 组件。

#### <a name="tessera"></a>Tessera

1. 安装 Java 11。 例如 `apt install default-jre`。
1. 更新 `tessera-config.json` 中的路径。 将 `/working-dir/**` 的所有引用更改为 `/opt/blockchain/data/working-dir/**`。
1. 根据新的 IP 地址更新其他事务节点的 IP 地址。 HTTPS 将不起作用，因为它未在 Tessera 配置中启用。 若要了解如何配置 TLS，请参阅 [Tessera 配置 TLS](https://docs.tessera.consensys.net/en/stable/HowTo/Configure/TLS/) 一文。
1. 更新 NSG 规则以允许到端口 9000 的入站连接。
1. 使用以下命令运行 Tessera：

    ```bash
    java -Xms512M -Xmx1731M -Dlogback.configurationFile=/tessera/logback-tessera.xml -jar tessera.jar -configfile /opt/blockchain/data/working-dir/tessera-config.json > tessera.log 2>&1 &
    ```

#### <a name="geth"></a>Geth

1. 更新 `/opt/blockchain/data/working-dir/dd/static-nodes.json` 中 enode 地址中的 IP。 允许公共 IP 地址。
1. 在 `/geth/config.toml` 中的 StaticNodes 项下进行相同的 IP 地址更改。
1. 更新 NSG 规则以允许到端口 30303 的入站连接。
1. 使用下列命令运行 Geth：

    ```bash
    export NETWORK_ID='' # Get network ID from metadata. The network ID is the same for consortium.

    PRIVATE_CONFIG=tm.ipc geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul
    ```

### <a name="validator-node"></a>验证器节点

验证器节点步骤类似于事务节点，只是 Geth 启动命令将具有附加标志 `-mine`。 Tessera 未在验证器节点上启动。 要在没有配对的 Tessera 的情况下运行 Geth，可以在 Geth 命令中传递 `PRIVATE_CONFIG=ignore`。 使用下列命令运行 Geth：

```bash
export NETWORK_ID=`j q '.APP_SETTINGS | fromjson | ."network-id"' env.json`

PRIVATE_CONFIG=ignore geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul –mine
```

## <a name="upgrading-quorum"></a>升级 Quorum

Azure 区块链服务可能正在运行以下列出的 Quorum 版本之一。 可以选择使用相同的 Quorum 版本或按照以下步骤使用最新版本的 ConsenSys Quorum。

### <a name="upgrade-quorum-version-260-or-270-to-consensys-2110"></a>将 Quorum 版本 2.6.0 或 2.7.0 升级到 ConsenSys 21.1.0

从 Quorum 2.6 或 2.7 版本升级非常简单。 使用以下链接下载和更新。
1. 下载 [ConsenSys Quorum 和相关二进制文件 v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0)。
1. 下载最新版本的 Tessera [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0)。

### <a name="upgrade-quorum-version-250-to-consensys-2110"></a>将 Quorum 版本 2.5.0 升级到 ConsenSys 21.1.0

1. 下载 [ConsenSys Quorum 和相关二进制文件 v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0)。
1. 下载最新版本的 Tessera [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0)。
对于版本 2.5.0，需要在 genesis 文件进行一些小的更改。 在 genesis 文件中进行以下更改。

1. 将值 `byzantiumBlock` 设置为 1，并且不能小于 `constantinopleBlock`，即 0。 将 `byzantiumBlock` 值设置为 0。
1. 将 `petersburgBlock`、`istanbulBlock` 设置为 future block。 此值在所有节点上都应相同。
1. 此步骤是可选的。 `ceil2Nby3Block` 被错误地放置在 Azure 区块链服务 Quorum 2.5.0 版本中。 它本应位于 istanbul 配置中，并要设置值 future block。 此值在所有节点上都应相同。
1. 使用以下命令运行 geth 以重新初始化 genesis block：

    ```bash
    geth --datadir "Data Directory Path" init "genesis file path"
    ```

1.  运行 Geth。

## <a name="exported-data-reference"></a>导出的数据参考

本节介绍元数据和文件夹结构，以帮助将数据导入 IaaS VM 部署。

### <a name="metadata-info"></a>元数据信息

| 名称               | 示例                | 说明           |
|--------------------|-----------------------|-----------------------|
| consortium_name    | \<ConsortiumName\>    | 联盟名称（在 Azure 区块链服务中唯一）。 |
| Consortium_Member_Count || 联盟中的成员数 |
| member_name        | \<memberName\>        | 区块链成员名称（在 Azure 区块链服务中唯一）。 |
| node_name          | transaction-node      | 节点名称（每个成员有多个节点）。 |
| network_id         | 543                   | Geth 网络 ID。      |
| is_miner           | 错误                 | Is_Miner == true（验证器节点），Is_Miner == false（事务节点） |
| quorum_version     | 2.7.0                 | Quorum 版本     |
| tessera_version    | 0.10.5                | Tessera 版本      |
| java_version       | java-11-openjdk-amd64 | Tessera 使用的 Java 版本 |
| CurrentBlockNumber |                       | 区块链网络的当前块数 |

## <a name="migrated-data-folder-structure"></a>迁移的数据文件夹结构

在顶层，有与成员的每个节点相对应的文件夹。

- **标准 SKU** - 两个验证器节点（validator-node-0 和 validator-node-1）
- **基本 SKU** - 一个验证器节点 (validator-node-0)
- **事务节点** - 名为 transaction-node 的默认事务节点。

其他事务节点文件夹以事务节点名称命名。

### <a name="node-level-folder-structure"></a>节点级文件夹结构

每个节点级文件夹都包含一个使用加密密钥加密的 zip 文件。 有关获取加密密钥的详细信息，请参阅本文的[下载数据](#download-data)部分。

| 目录/文件 | 说明 |
|----------------|--------------|
| /config/config.toml | Geth 参数。 命令行参数优先 |
| /config/genesis.json | Genesis 文件 |
| /config/logback-tessera.xml | Tessera 的 Logback 配置 |
| /config/static-nodes.json | 静态节点。 将删除启动节点并禁用自动发现。 |
| /config/tessera-config.json | Tessera 配置 |
| /data/c/ | Tessera DB |
| /data/dd/ | Geth 数据目录 |
| /env/env | 元数据 |
| /keys/ | Tessera 密钥 |
| /scripts/ | 启动脚本（仅供参考） |

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-does-service-retirement-mean-for-existing-customers"></a>服务停用对现有客户意味着什么？

2021 年 9 月 10 日之后将无法继续使用现有 Azure 区块链服务部署。 请根据你的需求在停用前开始评估本文中建议的备选方案。

### <a name="what-happens-to-existing-deployments-after-the-announcement-of-retirement"></a>宣布停用后，现有部署会发生什么情况？

对现有部署的支持截止到 2021 年 9 月 10 日。 评估建议的备选方案，将数据迁移到备选产品/服务，在备选产品/服务上根据需要操作，然后开始从 Azure 区块链服务上的部署迁移。

### <a name="how-long-will-the-existing-deployments-be-supported-on-azure-blockchain-service"></a>Azure 区块链服务支持现有部署多长时间？

对现有部署的支持截止到 2021 年 9 月 10 日。

### <a name="will-i-be-allowed-to-create-new-azure-blockchain-members-while-in-retirement-phase"></a>是否允许在停用阶段创建新的 Azure 区块链成员？

2021 年 5 月 10 日之后，不再支持创建或部署新成员。
