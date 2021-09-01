---
title: 配置 Azure 区块链服务事务节点
description: 如何配置 Azure 区块链服务事务节点
ms.date: 05/11/2021
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 436c7721bac29e8a18a333e385f12a70e0701ba5
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "122652585"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>配置 Azure 区块链服务事务节点

事务节点用于通过公共终结点向 Azure 区块链服务发送区块链事务。 默认事务节点包含在区块链上注册的 Ethereum 帐户的私钥，因此无法删除。

[!INCLUDE [Retirement note](./includes/retirement.md)]

查看默认事务节点详细信息：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到你的 Azure 区块链服务成员。 选择“事务列表”。

    ![选择默认事务节点](./media/configure-transaction-nodes/nodes.png)

    概述详细信息包括公共终结点地址和公钥。

## <a name="create-transaction-node"></a>创建事务节点

最多可以向区块链成员添加 9 个附加事务节点，总计 10 个事务节点。 通过添加事务节点，可以提高可伸缩性或分布负载。 例如，不同的客户端应用程序可以各有一个事务节点终结点。

添加事务节点：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，并选择“事务节点”>“添加”。
1. 完成新事务节点的设置。

    ![添加事务节点](./media/configure-transaction-nodes/add-node.png)

    | 设置 | 说明 |
    |---------|-------------|
    | 名称 | 事务节点名称。 该名称用于创建事务节点终结点的 DNS 地址。 例如，`newnode-myblockchainmember.blockchain.azure.com`。 节点名称一旦创建便无法再进行更改。 |
    | 密码 | 设置强密码。 使用密码通过基本身份验证访问事务节点终结点。

1. 选择“创建”  。

    预配新的事务节点大约需要 10 分钟时间。 附加事务节点会产生费用。 有关费用的详细信息，请参阅 [Azure 定价](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>终结点

事务节点具有唯一的 DNS 名称和公共终结点。

查看事务节点的终结点详细信息：

1. 在 Azure 门户中，导航到其中一个 Azure 区块链服务成员事务节点，并选择“概述”。

    ![屏幕截图显示了某个区块链成员的事务节点概述。](./media/configure-transaction-nodes/endpoints.png)

事务节点终结点是安全的，需要进行身份验证。 用户可以使用 Azure AD 身份验证、HTTPS 基本身份验证以及基于 HTTPS 的访问密钥或基于 TLS 的 Websocket 来连接到事务终结点。

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 访问控制

Azure 区块链服务事务节点终结点支持 Azure Active Directory (Azure AD) 身份验证。 可以向 Azure AD 用户、组和服务主体授予对终结点的访问权限。

授予对终结点的 Azure AD 访问控制权限：

1. 在 Azure 门户中，导航到 Azure 区块链服务成员，并选择“事务节点”>“访问控制 (IAM)”>“添加”>“添加角色分配”。
1. 为用户、组或服务主体（应用程序角色）创建新的角色分配。

    ![添加 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 设置 | 操作 |
    |---------|-------------|
    | 角色 | 选择“所有者”、“参与者”或“读者”。
    | 将访问权限分配到 | 选择“Azure AD 用户、组或服务主体”。
    | Select | 搜索要添加的用户、组或服务主体。

1. 选择“保存”以添加角色分配。

有关 Azure AD 访问控制的详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)

有关如何使用 Azure AD 身份验证进行连接的详细信息，请参阅[使用 AAD 身份验证连接节点](configure-aad.md)。

### <a name="basic-authentication"></a>基本身份验证

对于 HTTPS 基本身份验证，用户名和密码凭据会在请求的 HTTPS 标头中传递到终结点。

在 Azure 门户中可查看事务节点的基本身份验证终结点详细信息。 导航到其中一个 Azure 区块链服务成员事务节点，并在设置中选择“基本身份验证”。

![基本身份验证](./media/configure-transaction-nodes/basic.png)

用户名是节点的名称，无法更改。

若要使用 URL，请将 \<password\> 替换为预配节点时设置的密码。 可以通过选择“重置密码”来更新密码。

### <a name="access-keys"></a>访问键

对于访问密钥身份验证，访问密钥包含在终结点 URL 中。 预配事务节点后，将生成两个访问密钥。 使用任一密钥均可进行身份验证。 使用两个密钥可以更改和轮替密钥。

用户可以查看事务节点的访问密钥详细信息，并复制包含访问密钥的终结点地址。 导航到其中一个 Azure 区块链服务成员事务节点，并在设置中选择“访问密钥”。

### <a name="firewall-rules"></a>防火墙规则

使用防火墙规则，可以限制能够尝试对事务节点进行身份验证的 IP 地址。  如果没有对事务节点配置防火墙规则，则任何参与方都不能访问它。

要查看事务节点的防火墙规则，请导航到其中一个 Azure 区块链服务成员事务节点，并在设置中选择“防火墙规则”。

可以通过在“防火墙规则”网格中输入规则名称、起始 IP 地址和结束 IP 地址来添加防火墙规则。

![防火墙规则](./media/configure-transaction-nodes/firewall-rules.png)

若要启用：

* 单个 IP 地址：为起始 IP 地址和结束 IP 地址配置相同的 IP 地址。
* IP 地址范围：配置起始和结束 IP 地址的范围。 例如，从 10.221.34.0 开始到 10.221.34.255 结束的范围会启用整个 10.221.34.xxx 子网。
* 允许所有 IP 地址：将起始 IP 地址配置为 0.0.0.0，并将结束 IP 地址配置为 255.255.255.255。

## <a name="connection-strings"></a>连接字符串

为基本身份验证或使用访问密钥提供事务节点的连接字符串语法。 提供的连接字符串包括基于 HTTPS 的访问密钥和 WebSocket。

可以查看事务节点的连接字符串并复制终结点地址。 导航到其中一个 Azure 区块链服务成员事务节点，并在设置中选择“连接字符串”。

![连接字符串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>代码示例

提供的示例代码旨在通过 Web3、Nethereum、Web3js 和 Truffle 快速连接到事务节点。

可以查看事务节点的示例连接代码，并将其复制下来以便与常用的开发者工具结合使用。 转到其中一个 Azure 区块链服务成员事务节点，并在设置中选择“代码示例”。

选择 Web3、Nethereum、Truffle 或 Web3j 选项卡，可查看要使用的代码示例。

![代码示例](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 配置事务节点](manage-cli.md)
