---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623403"
---
* 创建新的工作区时，可以自动创建工作区所需的服务或使用现有的服务。 如果要使用来自不同于工作区所在的 Azure 订阅的现有服务，则必须在包含这些服务的订阅中注册 Azure 机器学习命名空间。 例如，在订阅 A 中创建一个使用订阅 B 中的存储帐户的工作区时，必须在订阅 B 中注册 Azure 机器学习命名空间，然后才能将此存储帐户用于该工作区。

    Azure 机器学习的资源提供程序是 Microsoft.MachineLearningServices。 有关如何查看它是否已注册以及如何注册的信息，请参阅 [Azure 资源提供程序和类型](../articles/azure-resource-manager/management/resource-providers-and-types.md)一文。

    > [!IMPORTANT]
    > 这仅适用于工作区创建期间提供的资源：Azure 存储帐户、Azure 容器注册表、Azure Key Vault 和 Application Insights。
