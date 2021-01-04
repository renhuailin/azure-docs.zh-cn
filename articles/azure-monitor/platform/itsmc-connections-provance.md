---
title: 将 Provance 连接 IT 服务管理连接器
description: 本文提供了有关如何在 Azure Monitor 中 IT 服务管理连接器 Provance (ITSMC) 的信息，以集中监视和管理 ITSM 工作项的信息。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 19d2ef1d4d711738d60938aac502dfd490e021a1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729607"
---
# <a name="connect-provance-with-it-service-management-connector"></a>将 Provance 连接 IT 服务管理连接器

本文提供了有关如何在 Provance 实例与 IT 服务管理连接器 (ITSMC) Log Analytics 中配置连接以集中管理工作项的信息。

> [!NOTE]
> 我们建议 Cherwell 和 Provance 客户使用 [Webhook 操作](./action-groups.md#webhook) 来 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

以下部分提供有关如何将 Provance 产品连接到 Azure 中的 ITSMC 的详细信息。

## <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](./itsmc-definition.md#add-it-service-management-connector)。
- Provance 应用应已注册到 Azure AD，并且可提供客户端 ID。 有关详细信息，请参阅[如何配置 Active Directory 身份验证](../../app-service/configure-authentication-provider-aad.md)。

- 用户角色：管理员。

## <a name="connection-procedure"></a>连接过程

使用以下过程创建 Provance 连接：

1. 在 Azure 门户中，转到“所有资源”，查找“ServiceDesk(YourWorkspaceName)”

2. 在“工作区数据源”下，单击“ITSM 连接”。
    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”。

4. 提供下表中所述的信息，并单击“确定”创建连接。

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要与 ITSMC 连接的 Provance 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“Provance”。 |
| **用户名**   | 键入可以连接到 ITSMC 的用户名。    |
| **密码**   | 键入此用户名的关联密码。 **注意：** 用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。|
| **服务器 URL**   | 键入需要连接到 ITSMC 的 Provance 实例的 URL。 |
| **客户端 ID**   | 键入在 Provance 实例中生成的、用于对此连接进行身份验证的客户端 ID。  有关客户端 ID 的详细信息，请参阅[如何配置 Active Directory 身份验证](../../app-service/configure-authentication-provider-aad.md)。 |
| **数据同步范围**   | 选择需要通过 ITSMC 同步到 Azure Log Analytics 的 Provance 工作项。  这些工作项将导入到 Log Analytics。   **选项：** “事件”、“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。|

![突出显示 "连接名称" 和 "合作伙伴类型" 列表的屏幕截图。](media/itsmc-connections/itsm-connections-provance-latest.png)

成功连接并同步后：

- 选定的工作项将从此 Provance 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Provance 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

## <a name="next-steps"></a>后续步骤

* [ITSM 连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)