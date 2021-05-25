---
title: Azure 区块链服务安全性
description: Azure 区块链服务数据访问和安全概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "80879592"
---
# <a name="azure-blockchain-service-security"></a>Azure 区块链服务安全性

Azure 区块链服务使用多种 Azure 功能来确保数据安全可用。 使用隔离、加密和身份验证来保护数据。

## <a name="isolation"></a>隔离

Azure 区块链服务资源隔离在专用虚拟网络中。 每个事务和验证节点都是一个虚拟机 (VM)。 一个虚拟网络中的 VM 无法直接与其他虚拟网络中的 VM 通信。 隔离可确保通信在虚拟网络内保持私密状态。 有关 Azure 虚拟网络隔离的详细信息，请参阅 [Azure 公有云中的隔离](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 关系图](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

用户数据存储在 Azure 存储中。 动态和静态的用户数据都进行了加密，以确保安全性和机密性。 有关详细信息，请参阅 [Azure 存储安全指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>身份验证

可以通过 RPC 终结点将事务发送到区块链节点。 客户端使用可处理用户身份验证并通过 TLS 加密数据的反向代理服务器与事务节点进行通信。

![身份验证示意图](./media/data-security/authentication.png)

可通过三种身份验证模式访问 RPC。

### <a name="basic-authentication"></a>基本身份验证

基本身份验证使用包含用户名和密码的 HTTP 身份验证标头。 用户名是区块链节点的名称。 在预配成员或节点期间设置密码。 可以使用 Azure 门户或 CLI 更改密码。

### <a name="access-keys"></a>访问键

访问密钥使用终结点 URL 中包含的随机生成的字符串。 两个访问密钥有助于实现密钥轮换。 可以从 Azure 门户和 CLI 重新生成密钥。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 使用基于声明的身份验证机制，在该机制中，用户通过 Azure AD 使用 Azure AD 用户凭据进行身份验证。 Azure AD 提供基于云的标识管理，允许客户在整个企业中使用单个标识，并访问云中的应用程序。 Azure 区块链服务与 Azure AD 集成，可支持 ID 联合身份验证、单一登录和多重身份验证。 你可以为区块链成员和节点访问分配组织中的用户、组和应用程序角色。

[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases) 上提供了 Azure AD 客户端代理。 客户端代理将用户定向到 Azure AD 登录页面，并在身份验证成功后获得持有者令牌。 随后，用户将 Geth 或 Truffle 等 Ethereum 客户端应用程序连接到客户端代理的终结点。 最后，在提交事务时，客户端代理将持有者令牌注入 http 标头，反向代理使用 OAuth 协议验证令牌。

## <a name="keys-and-ethereum-accounts"></a>密钥和 Ethereum 帐户

预配 Azure 区块链服务成员时将生成 Ethereum 帐户以及公钥和私钥对。 私钥用于将交易发送到区块链。 Ethereum 帐户是公钥哈希的最后 20 个字节。 Ethereum 帐户又称为钱包。

私钥和公钥对以 JSON 格式存储为密钥文件。 使用创建区块链账本服务时输入的密码对私钥进行加密。

私钥用于对事务进行数字签名。 在专用区块链中，使用私钥签名的智能合同表示签名者的标识。 若要验证签名是否有效，接收方可以对比签名者的公钥与根据签名计算出的地址。

Constellation 密钥用于唯一地标识 Quorum 节点。 Constellation 密钥在节点预配时生成，在 Quorum 中私有事务的 privateFor 参数中指定。

## <a name="next-steps"></a>后续步骤

请参阅[如何为 Azure 区块链服务配置 Azure Active Directory 访问权限](configure-aad.md)。
