---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 09/14/2021
ms.author: larryfr
ms.openlocfilehash: 757179959035c6bfce77b1feaaf5bfeff5aab001
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621580"
---
> [!IMPORTANT]
> 此 Cosmos DB 实例是在订阅的 Microsoft 受管理资源组中创建而成。 以下服务也是在此资源组中创建，并且由客户管理的密钥配置使用：
> * Azure 存储帐户
> * Azure 搜索
>
> 由于这些服务是在你的 Azure 订阅中创建，因此这意味着你需要支付创建这些服务实例的费用。 如果订阅没有足够的 Azure Cosmos DB 服务配额，将会发生故障。 有关配额的详细信息，请参阅 [Azure Cosmos DB 设备配额](/azure/cosmos-db/concepts-limits)
>
> 托管资源组的命名格式为 `<AML Workspace Resource Group Name><GUID>`。 如果 Azure 机器学习工作区使用专用终结点，则系统还将在此资源组中创建一个虚拟网络。 此 VNet 用于保护此资源组和 Azure 机器学习工作区中的服务之间的通信。
> 
> * 请勿删除包含此 Cosmos DB 实例的资源组，也不要删除此组中自动创建的任何资源。 如果需要删除该资源组和 Cosmos DB 实例等内容，必须删除使用它的 Azure 机器学习工作区。 删除与资源组、Cosmos DB 实例和其他自动创建的资源相关联的工作区时，这些资源都将被删除。
> * 此 Cosmos DB 帐户使用的[请求单位](../articles/cosmos-db/request-units.md)会根据需要自动缩放。 最小 RU 为 1200。 最大 RU 为 12000。
> * 不能提供自己的 VNet 来与创建的 Cosmos DB 实例一起使用。 也不能修改虚拟网络。 例如，你不能更改它使用的 IP 地址范围。
> 
> 若要估算 Azure Cosmos DB 实例的额外费用，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。