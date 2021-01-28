---
title: 常见错误
description: 本文档包含有关仪表板中存在的常见错误的信息
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: be6d47d8f40746bfb2154ddb62cf2e9ce93e74aa
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955677"
---
# <a name="errors-in-the-connector-status-section"></a>"连接器状态" 部分中的错误

在仪表板的 "连接器状态列表" 部分中，可以找到可帮助解决 ITSM 连接器中的问题的错误。

## <a name="status-common-errors"></a>状态常见错误

在本部分中，可以找到 "连接器状态" 部分中显示的常见错误以及如何解决这些错误：

* **错误**： "从 ServiceNow 发出意外响应，以及成功状态代码。 响应： {"import_set"： "{import_set_id}"、"staging_table"： "x_mioms_microsoft_oms_incident"、"result"： [{"transform_map"： "OMS 事件"、"表"： "事件"、"状态"： "错误"、"error_message"： "{找不到目标记录 |无效的表 |临时表 "}" 无效

    **原因**：当以下情况时，ServiceNow 返回了此类错误：
  * 在 ServiceNow 实例中部署的自定义脚本将导致事件被忽略。
  * 在 ServiceNow 端修改了 "OMS 集成器应用" 代码本身，例如 onBefore 脚本。

  **解决方法**：禁用所有自定义脚本或代码修改。

* **错误**： "{" 错误 "： {" message "：" 操作失败 "，" 详细信息 "：" 由于安全约束，ACL 异常更新失败 "

    **原因**： ServiceNow 权限配置错误

    **解决方法**：检查是否已根据 [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)正确分配所有角色。

* **错误**： "发送请求时出错。"

    **原因**： "ServiceNow 实例不可用"

    **解决方法**：检查 ServiceNow 中的实例，它可能已被删除或不可用。

* **错误**： "ServiceDeskHttpBadRequestException： StatusCode = 429"

    **原因**： ServiceNow 速率限制太高/低。

    **解决方法**：在 ServiceNow 实例中增加或取消速率限制，如 [此处](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)所述。

* **错误**： "AccessToken 和 RefreshToken 无效。 用户需要重新进行身份验证。 "

    **原因**：刷新令牌已过期。

    **解决方法**：同步 ITSM 连接器以生成新的刷新令牌，如 [此处](./itsmc-resync-servicenow.md)所述。

* **错误**： "无法创建/更新警报 {alertName} 的工作项。 ITSM 连接器 {connectionIdentifier} 不存在或已被删除。 "

    **原因**：已删除 ITSM 连接器。

    **解决方法**：已删除 ITSM 连接器，但定义了与之相关联的 ITSM 操作组。 可以通过2个选项来解决此问题：
  * 查找和禁用或删除此类操作组
  * [重新配置操作组](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 以使用现有 ITSM 连接器。
  * [创建新的 ITSM 连接器](./itsmc-definition.md#create-an-itsm-connection) ，并 [重新配置操作组以使用该](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)连接器。

## <a name="ui-common-errors"></a>UI 常见错误

* **错误**： "出现错误。 无法获取连接详细信息。 " 当客户定义 ITSM 操作组时，会出现此错误。

    **原因**：当以下情况时，将显示此类错误：
    * 新创建的 ITSM 连接器尚未完成初始同步。
    * 连接器未正确定义

    **解决方法**： 
    * 创建新的 ITSM 连接器后，ITSM 连接器会开始同步 ITSM 系统中的信息，例如工作项模板和工作项。 同步 ITSM 连接器以生成新的刷新令牌，如 [此处](./itsmc-resync-servicenow.md)所述。
    * 查看 ITSM 连接器中的连接详细信息，如 [此处](./itsmc-connections-servicenow.md#create-a-connection) 所述，检查 ITSM 连接器是否可以成功 [同步](./itsmc-resync-servicenow.md)。
