---
title: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 24bc49b82c24c560dea12c744097f643bec2b3f5
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904363"
---
向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

以下列表描述可以限定 Azure blob 资源访问权限范围的等级，从最窄的范围开始：

- **单个容器。** 在此范围内，角色分配将应用于容器中的所有 blob，以及容器属性和元数据。
- **存储帐户。** 在此范围内，角色分配将应用于所有容器及其 blob。
- **资源组。** 在此范围内，角色分配适用于资源组内的所有存储帐户中的所有容器。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有容器。

有关 Azure RBAC 角色分配范围的详细信息，请参阅[了解 Azure RBAC 的范围](../articles/role-based-access-control/scope-overview.md)。
