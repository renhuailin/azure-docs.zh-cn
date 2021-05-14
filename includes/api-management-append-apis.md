---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 890cb1dc8d8a59f6b567497b968b53bb89ec1ecc
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108230547"
---
## <a name="append-other-apis"></a>追加其他 API

可以为不同服务公开的 API 撰写 API，包括：
* OpenAPI 规范
* SOAP API
* Azure 应用服务中托管的 Web 应用
* Azure 函数应用
* Azure 逻辑应用
* Azure Service Fabric

按照以下步骤将其他 API 追加到现有 API。 

>[!NOTE] 
> 导入另一个 API 时，操作将追加到当前 API 后面。

1. 在 Azure 门户中转到自己的 Azure API 管理实例。

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="转到 Azure API 管理实例":::

1. 在概述页面上或左侧的菜单上选择“API” 。

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="选择“API”":::

1. 单击要将另一个 API 追加到的 API 旁边的“...”。
1. 从下拉菜单中选择“导入”。

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="选择“导入”":::

1. 选择要从中导入 API 的服务。

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="选择服务":::
