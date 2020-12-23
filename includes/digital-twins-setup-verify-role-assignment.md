---
author: baanders
description: 用于验证 Azure 数字孪生安装程序中的角色分配的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489026"
---
检查是否已成功设置角色分配的一种方法是在 [Azure 门户](https://portal.azure.com)中查看 Azure 数字孪生实例的角色分配。 在 Azure 门户中中转到 Azure 数字孪生实例 (可以在 [Azure 数字孪生实例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 的页面上查找，也可以在门户搜索栏中搜索其名称) 。

然后，在 "访问控制" 下查看其所有已分配的角色 * (IAM) > 角色分配*。 用户应显示在列表中，其中包含 *Azure 数字孪生数据所有者*的角色。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 门户中查看 Azure 数字孪生实例的角色分配":::