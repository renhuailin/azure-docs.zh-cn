---
title: 将 Cherwell 连接到 IT 服务管理连接器
description: 本文介绍如何将 Cherwell 与 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 相连接，从而集中监视和管理 ITSM 工作项。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 0f488b047cf1323619d9603204877a55b413c295
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802465"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>将 Cherwell 连接到 IT 服务管理连接器

本文介绍如何配置 Cherwell 实例与 Log Analytics 中的 IT 服务管理连接器 (ITSMC) 之间的连接，以便集中管理工作项。

> [!NOTE]
> 自 2020 年 10 月 1 日起，将不再为新客户提供 Cherwell ITSM 与 Azure 警报集成服务。 将不支持新的 ITSM 连接。
> 将支持现有的 ITSM 连接。

以下部分提供有关如何将 Cherwell 产品连接到 Azure 中的 ITSMC 的详细信息。

## <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](./itsmc-definition.md#add-it-service-management-connector)。
- 已生成客户端 ID。 详细信息：[为 Cherwell 生成客户端 ID](#generate-client-id-for-cherwell)。
- 用户角色：管理员。

## <a name="connection-procedure"></a>连接过程

使用以下过程创建 Cherwell 连接：

1. 在 Azure 门户中，转到“所有资源”，查找“ServiceDesk(YourWorkspaceName)”

2. 在“工作区数据源”下，单击“ITSM 连接”。
    ![新建连接](/azure/azure-monitor/alerts/media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”。

4. 提供下表中所述的信息，并单击“确定”创建连接。

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要连接到 ITSMC 的 Cherwell 实例名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“Cherwell”。 |
| **用户名**   | 键入可以连接到 ITSMC 的 Cherwell 用户名。 |
| **密码**   | 键入此用户名的关联密码。 **注意：** 用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。|
| **服务器 URL**   | 键入需要连接到 ITSMC 的 Cherwell 实例的 URL。 |
| **客户端 ID**   | 键入在 Cherwell 实例中生成的、用于对此连接进行身份验证的客户端 ID。   |
| **数据同步范围**   | 选择需要通过 ITSMC 同步的 Cherwell 工作项。  这些工作项将导入到 Log Analytics。   **选项：** “事件”、“更改请求”。 |
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

![Cherwell 连接](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

成功连接并同步后：

- 选定的工作项将从此 Cherwell 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Cherwell 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

了解详细信息：[根据 Azure 警报创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

### <a name="generate-client-id-for-cherwell"></a>为 Cherwell 生成客户端 ID

若要为 Cherwell 生成客户端 ID/密钥，请使用以下过程：

1. 以管理员身份登录到 Cherwell 实例。
2. 单击“安全性” > “编辑 REST API 客户端设置”。 
3. 选择“创建新客户端” > “客户端机密”。 

    ![Cherwell 用户 ID](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>后续步骤

* [IT 服务管理连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
