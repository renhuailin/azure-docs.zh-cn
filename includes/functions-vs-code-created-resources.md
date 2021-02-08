---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493432"
---
+ 一个[资源组](../articles/azure-resource-manager/management/overview.md)：相关资源的逻辑容器。
+ 一个标准 [Azure 存储帐户](../articles/storage/common/storage-account-create.md)：用于维护项目的状态和其他信息。
+ 一个消耗计划：用于定义无服务器函数应用的基础主机。 
+ 一个函数应用：提供用于执行函数代码的环境。 可以通过函数应用将函数分组为逻辑单元，以便在同一托管计划中更轻松地管理、部署和共享资源。
+ 一个连接到函数应用的 Application Insights 实例：用于跟踪无服务器函数的使用情况。