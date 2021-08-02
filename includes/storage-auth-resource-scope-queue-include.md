---
title: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 7f2b15a57b42f87e749060f510dafff732a1d9e0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904368"
---
向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

以下列表描述可以限定 Azure 队列资源访问权限范围的等级，从最窄的范围开始：

- **单个队列。** 在此范围内，角色分配将应用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配将应用于所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组内的所有存储帐户中的所有队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有队列。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有队列。

有关 Azure RBAC 角色分配范围的详细信息，请参阅[了解 Azure RBAC 的范围](../articles/role-based-access-control/scope-overview.md)。
