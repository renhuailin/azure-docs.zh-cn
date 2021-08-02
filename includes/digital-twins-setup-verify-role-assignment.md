---
author: baanders
description: 在 Azure 数字孪生安装程序中用于验证角色分配的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 044342122c2bd1d2b59a7fc3abb4ed6ca7bbc05f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110473799"
---
检查是否已成功设置角色分配的一种方法是在 [Azure 门户](https://portal.azure.com)中查看 Azure 数字孪生实例的角色分配。 在 Azure 门户中转到 Azure 数字孪生实例（可以在 [Azure 数字孪生实例](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)页上查找它，也可以在门户搜索栏中搜索它的名称）。

然后，在“访问控制(IAM)”>“角色分配”下查看其已分配的所有角色。 该用户应显示在列表中，其角色为“Azure 数字孪生数据所有者”。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure 门户中 Azure 数字孪生实例的角色分配屏幕截图。":::