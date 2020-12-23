---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445208"
---
* 创建新的工作区时，可以自动创建工作区所需的服务或使用现有的服务。 如果要使用不同于工作区的 __Azure 订阅中的现有服务__ ，则必须在包含这些服务的订阅中注册 Azure 机器学习命名空间。 例如，在订阅 A 中创建使用订阅 B 中的存储帐户的工作区时，必须先在订阅 B 中注册 Azure 机器学习命名空间，然后才能将该存储帐户与工作区结合使用。

    Azure 机器学习的资源提供程序是 __MachineLearningService__。 有关如何查看它是否已注册以及如何注册的信息，请参阅 [Azure 资源提供程序和类型](../articles/azure-resource-manager/management/resource-providers-and-types.md)  一文。

    > [!IMPORTANT]
    > 这仅适用于在创建工作区期间提供的资源;Azure 存储帐户、Azure 容器注册、Azure Key Vault 和 Application Insights。