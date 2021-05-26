---
title: Azure 机密账本体系结构
description: Azure 机密账本体系结构
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: b7403cec5df1ac5f7d5a313739f597494b7c48ee
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384930"
---
# <a name="architecture"></a>体系结构

使用 Azure 机密账本（REST API 服务），用户可以通过管理和功能 API 调用与账本进行交互。  数据记录到账本时，会被发送到授权区块链节点，这些节点是安全的 Enclave 备份副本。 副本遵循共识概念。 用户还可以检索已提交到账本的数据的回执。

此外，还提供了一个可选的联盟概念，将来可支持多方协作。

## <a name="architecture-diagram"></a>体系结构关系图

此映像提供 Azure 机密账本的体系结构概述，并显示与创建的账本的云 API 交互的 Azure 机密账本用户。

:::image type="content" source="./media/architecture-overview.png" alt-text="体系结构概述":::

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)
- [对 Azure 机密账本节点进行身份验证](authenticate-ledger-nodes.md)
