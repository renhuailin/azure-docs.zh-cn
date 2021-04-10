---
title: ITSMC 仪表板中的连接器状态错误
description: 了解 IT 服务管理连接器仪表板中存在的常见错误。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471527"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>ITSMC 仪表板中的连接器状态错误

IT 服务管理连接器 (ITSMC) 仪表板显示错误，可帮助你解决连接器中的问题。

以下部分描述了仪表板连接器状态部分中出现的常见错误，以及如何解决它们。

## <a name="unexpected-response"></a>意外的响应

错误：“来自 ServiceNow 的意外响应以及成功状态代码。 响应: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map": "OMS Incident", "table": "incident", "status": "error", "error_message": "{Target record not found|Invalid table|Invalid staging table" }”

原因：出现以下情况时，ServiceNow 会返回此错误：

* 在 ServiceNow 实例中部署的自定义脚本导致事件被忽略。
* “OMS 集成器应用”代码已在 ServiceNow 端进行了修改（例如，通过 `onBefore` 脚本）。

解决方法：禁用所有自定义脚本或代码修改。

## <a name="exception-update-failure"></a>异常更新失败

错误：“{"error":{"message":"Operation Failed","detail":"ACL Exception Update Failed due to security constraints"}”

原因：ServiceNow 权限配置错误。

解决方案：检查所有角色是否按[指定的方式](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)正确分配。

## <a name="problem-sending-a-request"></a>发送请求时出现问题

错误：“发送请求时出错。”

原因：ServiceNow 实例不可用。

解决方法：检查 ServiceNow 中的实例。 它可能已被删除或不可用。

## <a name="servicenow-rate-problem"></a>ServiceNow 速率问题

错误：“ServiceDeskHttpBadRequestException: StatusCode=429”

原因：ServiceNow 速率限制太大或过低。

解决方法：增加或取消 ServiceNow 实例中的速率限制，如 [ServiceNow 文档](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)中所述。

## <a name="invalid-refresh-token"></a>无效刷新令牌

**错误**： 
  * “AccessToken 和 RefreshToken 无效。 用户需要重新进行身份验证。”
  * “无法同步事件、警报和事件的模板配置。 有关详细信息，请参见异常消息。”

原因：刷新令牌已过期。

解决方法：同步 ITSMC 以生成新的刷新令牌，如[如何手动修复同步问题](./itsmc-resync-servicenow.md)中所述。

## <a name="missing-connector"></a>缺少连接器

错误：“无法创建/更新警报 {alertName} 的工作项。 ITSM 连接器 {connectionIdentifier} 不存在或已被删除。”

原因：已删除 ITSMC。

解决方法：已删除 ITSMC，但定义的 IT 服务管理 (ITSM) 操作组仍与其关联。 解决这一问题有以下三种选项：

* 查找和禁用/删除此类操作组。
* [重新配置操作组](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 以使用现有的 ITSMC 实例。
* [创建新 ITSMC 实例](./itsmc-definition.md#create-an-itsm-connection)并[重新配置操作组以使用它](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="lack-of-connection-details"></a>缺少连接详细信息

错误：“出现错误。 无法获取连接详细信息。” 定义 ITSM 操作组时，会出现此错误。

原因：此类错误在以下任一情况下出现：

* 新创建的 ITSM 连接器实例尚未完成初始同步。
* 连接器未正确定义。

**解决方法**： 

* 创建新的 ITSMC 实例时，它开始同步来自 ITSM 系统的信息，例如工作项目模板和工作项。 [同步 ITSMC 以生成新的刷新令牌](./itsmc-resync-servicenow.md)。
* [查看 ITSMC 中的连接详细信息](./itsmc-connections-servicenow.md#create-a-connection)，并检查 ITSMC 是否可以成功[同步](./itsmc-resync-servicenow.md)。


## <a name="ip-restrictions"></a>IP 限制
错误：“由于错误的请求，未能添加名为 "XXX" 的 ITSM 连接。 错误: 错误的请求。 为连接提供的参数无效。 Http 异常: 状态代码已禁止。”

原因：ITSM 应用程序的 IP 地址不允许来自合作伙伴 ITSM 工具的 ITSM 连接。

解决方案：为了列出 ITSM IP 地址以便允许来自合作伙伴 ITSM 工具的 ITSM 连接，建议列出他们 LogAnalytics 工作区所属的 Azure 区域的整个公共 IP 范围。 [在此处查看详细信息](https://www.microsoft.com/download/details.aspx?id=56519) 对于 EUS/WEU/EUS2/WUS2/美国中南部区域，客户只能列出 ActionGroup 网络标记。
