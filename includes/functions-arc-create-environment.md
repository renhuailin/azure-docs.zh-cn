---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 43eda9f0f5cdb108c2949205d72433c9d7176571
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371800"
---
## <a name="create-an-app-service-kubernetes-environment"></a>创建应用服务 Kubernetes 环境

在开始之前，必须为已启用 Azure Arc 的 Kubernetes 群集[创建应用服务 Kubernetes 环境](../articles/app-service/manage-create-arc-environment.md)。 

> [!NOTE] 
> 创建环境时，请确保同时记下自定义位置名称和包含该自定义位置的资源组名称。 你可以使用它们来查找自定义位置 ID，在环境中创建函数应用时需要用到该 ID。 
>
> 如果未创建环境，请与群集管理员联系。