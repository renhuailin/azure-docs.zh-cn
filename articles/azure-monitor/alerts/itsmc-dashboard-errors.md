---
title: ITSMC 仪表板中的连接器状态错误
description: 了解 IT 服务管理连接器仪表板中存在的常见错误。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 5cc3c4a07cc698f3592a2ff2fd76e9f4bbef441b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036446"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>ITSMC 仪表板中的连接器状态错误

IT 服务管理连接器 (ITSMC) "仪表板" 提供的错误可帮助解决连接器中的问题。

以下各节描述了在仪表板的 "连接器状态" 部分中出现的常见错误以及如何解决这些错误。

## <a name="unexpected-response"></a>意外响应

**错误**： "从 ServiceNow 发出意外响应，以及成功状态代码。 响应： {"import_set"： "{import_set_id}"、"staging_table"： "x_mioms_microsoft_oms_incident"、"result"： [{"transform_map"： "OMS 事件"、"表"： "事件"、"状态"： "错误"、"error_message"： "{找不到目标记录 |无效的表 |临时表 "}" 无效

**原因**：在以下情况下： ServiceNow 返回此错误：

* 在 ServiceNow 实例中部署的自定义脚本将导致事件被忽略。
* "OMS 集成器应用" 代码已在 ServiceNow 端 (例如，通过脚本) 进行了修改 `onBefore` 。

**解决方法**：禁用所有自定义脚本或代码修改。

## <a name="exception-update-failure"></a>异常更新失败

**错误**： "{" 错误 "： {" message "：" 操作失败 "，" 详细信息 "：" 由于安全约束，ACL 异常更新失败 "

**原因**： ServiceNow 权限配置错误。

**解决方法**：检查是否所有角色都按 [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)正确分配。

## <a name="problem-sending-a-request"></a>发送请求时出现问题

**错误**： "发送请求时出错。"

**原因**： ServiceNow 实例不可用。

**解决方法**：检查 ServiceNow 中的实例。 它可能已被删除或不可用。

## <a name="servicenow-rate-problem"></a>ServiceNow 速率问题

**错误**： "ServiceDeskHttpBadRequestException： StatusCode = 429"

**原因**： ServiceNow 速率限制太大或过低。

**解决方法**：增加或取消 servicenow 实例中的速率限制，如 [servicenow 文档](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)中所述。

## <a name="invalid-refresh-token"></a>刷新令牌无效

**错误**： 
  * "AccessToken 和 RefreshToken 无效。 用户需要重新进行身份验证。 "
  * "无法同步事件、警报和事件的模板配置。 有关更多详细信息，请参阅异常消息。

**原因**：刷新令牌已过期。

**解决方法**：同步 ITSMC 以生成新的刷新令牌，如 [如何手动修复同步问题](./itsmc-resync-servicenow.md)中所述。

## <a name="missing-connector"></a>缺少连接器

**错误**： "无法创建/更新警报 {alertName} 的工作项。 ITSM 连接器 {connectionIdentifier} 不存在或已被删除。 "

**原因**：已删除 ITSMC。

**解决方法**：已删除 ITSMC，但定义的 It 服务管理 (ITSM) 操作组仍与其关联。 有三个选项可解决此问题：

* 查找和禁用或删除此类操作组。
* [重新配置操作组](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 以使用现有的 ITSMC 实例。
* [创建新的 ITSMC 实例](./itsmc-definition.md#create-an-itsm-connection) ，并 [重新配置操作组以使用它](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="lack-of-connection-details"></a>缺少连接详细信息

**错误**： "出现错误。 无法获取连接详细信息。 " 定义 ITSM 操作组时，会出现此错误。

**原因**：此类错误在以下任一情况下出现：

* 新创建的 ITSM 连接器实例尚未完成初始同步。
* 连接器未正确定义。

**解决方法**： 

* 创建新的 ITSMC 实例后，它将开始从 ITSM 系统同步信息，如工作项模板和工作项。 [同步 ITSMC 以生成新的刷新令牌](./itsmc-resync-servicenow.md)。
* [查看 ITSMC 中的连接详细信息](./itsmc-connections-servicenow.md#create-a-connection) ，并检查 ITSMC 是否可以成功 [同步](./itsmc-resync-servicenow.md)。
