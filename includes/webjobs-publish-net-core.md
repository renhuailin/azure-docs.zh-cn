---
title: 包含文件
description: include 文件
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9013095a974a86d34399ab566bcc3fea440f2dc7
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125938"
---
1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”。

1. 在“发布”对话框中，对于“目标”，请选择“Azure”，然后选择“下一步”   。 

1. 对于“特定目标”，请选择“Azure WebJobs”，然后选择“下一步”  。

1. 在“应用服务实例”上方，选择“加号”(+) 按钮以创建新的 Azure WebJob。

1. 在“应用服务(Windows)”对话框中，使用下表中的主机设置。

    | 设置      | 建议的值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 |
    | **订阅** | 选择订阅 | 要使用的 Azure 订阅。 |
    | **[资源组](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  要在其中创建函数应用的资源组的名称。 选择“新建”  创建新的资源组。|
    | **[托管计划](../articles/app-service/overview-hosting-plans.md)** | 应用服务计划 | [应用服务计划](../articles/app-service/overview-hosting-plans.md)指定托管应用的 Web 服务器场的位置、大小和功能。 可以在托管多个应用时节省资金，方法是将多个 Web 应用配置为共用单个应用服务计划。 应用服务计划定义区域、实例大小、规模计数和 SKU（免费、共享、基本、标准或高级）。 然后，选择“新建”以创建一个新的应用服务计划。 免费层和基本层不支持“始终启用”选项以保持站点的持续运行。 |

    ![“创建应用服务”对话框](./media/webjobs-publish-netcore/app-service-dialog.png)

1. 选择“创建”以使用这些设置在 Azure 中创建 WebJob 和相关资源，并部署项目代码。

1. 选择“完成”以回到“发布”页。  