---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94574421"
---
> [!IMPORTANT]
> 此 Cosmos DB 实例及其所需的全部资源是在订阅的 Microsoft 托管资源组中创建的。 这意味着需要为此 Cosmos DB 实例付费。 托管资源组的命名格式为 `<AML Workspace Resource Group Name><GUID>`。 如果 Azure 机器学习工作区使用专用终结点，则还会为 Cosmos DB 实例创建一个虚拟网络。 此 VNet 用于保护 Cosmos DB 与 Azure 机器学习之间的通信。
> 
> * 请勿删除包含此 Cosmos DB 实例的资源组，也不要删除此组中自动创建的任何资源。 如果需要删除该资源组和 Cosmos DB 实例等内容，必须删除使用它的 Azure 机器学习工作区。 删除与资源组、Cosmos DB 实例和其他自动创建的资源相关联的工作区时，这些资源都将被删除。
> * 此 Cosmos DB 帐户的默认[请求单位数](../articles/cosmos-db/request-units.md)设置为“8000” 。
> * 不能提供自己的 VNet 来与创建的 Cosmos DB 实例一起使用。 也不能修改虚拟网络。 例如，你不能更改它使用的 IP 地址范围。