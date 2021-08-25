---
title: 使用 Geth 附加到 Azure 区块链服务
description: 附加到 Azure 区块链服务事务节点上的 Geth 实例
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 01cd813a9efd814837783343cef6a7fefd906e7f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "122643068"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>快速入门：使用 Geth 附加到 Azure 区块链服务事务节点

在本快速入门中，你将使用 Geth 客户端附加到 Azure 区块链服务事务节点上的 Geth 实例。 附加后，使用 Geth 控制台调用 Ethereum JavaScript API。

[!INCLUDE [Retirement note](./includes/retirement.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 安装 [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* 完整[快速入门：使用 Azure 门户创建区块链成员](create-member.md)或[快速入门：通过 Azure CLI 创建 Azure 区块链服务区块链成员](create-member-cli.md)

## <a name="get-geth-connection-string"></a>获取 Geth 连接字符串

可以在 Azure 门户中获取 Azure 区块链服务事务节点的 Geth 连接字符串。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到你的 Azure 区块链服务成员。 选择“事务节点”和默认事务节点链接。

    ![选择默认事务节点](./media/connect-geth/transaction-nodes.png)

1. 选择“连接字符串”。
1. 从“HTTPS(访问密钥 1)”中复制连接字符串。 下一部分中需要使用此字符串。

    ![连接字符串](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>连接到 Geth

1. 打开命令提示符或 shell。
1. 使用 Geth attach 子命令附加到你的事务节点上正在运行的 Geth 实例。 将连接字符串粘贴为 attach 子命令的参数。 例如：

    ``` bash
    geth attach <connection string>
    ```

1. 连接到事务节点的 Ethereum 控制台后，可以使用 Ethereum JavaScript API。

    例如，使用以下 API 来找到 chainId。

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    在此示例中，chainId 为 661。

    ![Azure 区块链服务选项](./media/connect-geth/geth-attach.png)

1. 若要从控制台断开连接，请键入 `exit`。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Geth 客户端附加到了 Azure 区块链服务事务节点上的 Geth 实例。 尝试下一个教程，使用适用于 Ethereum 的 Azure 区块链开发工具包通过事务创建、生成、部署和执行智能合同函数。

> [!div class="nextstepaction"]
> [在 Azure 区块链服务中创建、生成和部署智能合同](send-transaction.md)
