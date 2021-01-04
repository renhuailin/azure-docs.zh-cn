---
title: 连接 ServiceNow 与 IT 服务管理连接器
description: 本文提供了有关如何在 Azure Monitor 中使用 IT 服务管理连接器 (ITSMC) 进行 ServiceNow 讨论来集中监视和管理 ITSM 工作项的信息。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729604"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>连接 ServiceNow 与 IT 服务管理连接器

本文提供了有关如何配置 ServiceNow 实例与 IT 服务管理连接器 (ITSMC) Log Analytics 中的连接以集中管理工作项的信息。

以下部分提供有关如何将 ServiceNow 产品连接到 Azure 中的 ITSMC 的详细信息。

## <a name="prerequisites"></a>先决条件
请确保满足以下先决条件：
- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](./itsmc-definition.md#add-it-service-management-connector)。
- ServiceNow 支持的版本：奥兰多、纽约、马德里、伦敦、Kingston、雅加达、伊斯坦布尔、赫尔辛基、Geneva。
- 现在，从 Azure Monitor 发送的警报可以在 ServiceNow 中创建以下元素之一：事件、事件或警报。
> [!NOTE]
> ITSMC 仅支持 ServiceNow 的官方 SaaS 产品/服务。 当前不支持 ServiceNow 的专用部署。 

**ServiceNow 管理员必须在其 ServiceNow 实例中执行以下操作**：
- 生成 ServiceNow 产品的客户端 ID 和客户端密码。 有关如何生成客户端 ID 和机密的信息，请根据需要参阅以下信息：

    - [为奥兰多设置 OAuth](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为纽约设置 OAuth](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为马德里设置 OAuth](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为伦敦设置 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为金斯顿设置 OAuth](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为雅加达设置 OAuth](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为伊斯坦布尔设置 OAuth](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为赫尔辛基设置 OAuth](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [为日内瓦设置 OAuth](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> 在定义“设置 OAuth”的过程中，我们建议：
>
> 1) **将刷新令牌的生命期更新为 90 天 (7,776,000 秒)：** 在 [设置 OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) 的阶段 2：[为客户端创建用于访问实例的终结点](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) 定义终结点之后，在“ServiceNow”边栏选项卡中搜索“系统 OAuth”，而不是选择“应用程序注册表”。 选择已定义的 OAuth 的名称，并将“刷新令牌生命期”字段更新为 7,776,000（以秒为单位，即 90 天）。
> 最后单击“更新”。
> 2) **建议建立一个内部程序，用于确保连接保持活动状态：** 根据刷新令牌生命期来刷新令牌。 请确保在刷新令牌的预期到期时间之前（建议在刷新令牌生命期到期前几天）执行以下操作：
>
>     1. [完成 ITSM 连接器配置的手动同步过程](./itsmc-resync-servicenow.md)
>     2. 撤销旧的刷新令牌，因为出于安全考虑，不建议保留旧密钥。 在“ServiceNow”边栏选项卡中搜索“系统 OAuth”，而不是选择“管理令牌”。 根据 OAuth 名称和到期日期从列表中选取旧令牌。
> ![SNOW 系统 OAuth 定义](media/itsmc-connections/snow-system-oauth.png)
>     3. 单击“撤销访问权限”，然后单击“撤消”。

- 安装用于 Microsoft Log Analytics 集成的用户应用（ServiceNow 应用）。 [了解详细信息](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )。
> [!NOTE]
> ITSMC 仅支持从 ServiceNow 存储下载的适用于 Microsoft Log Analytics 集成的官方用户应用。 ITSMC 不支持 ServiceNow 端或不属于官方 ServiceNow 解决方案一部分的应用程序中的任何代码引入。 
- 为安装的用户应用创建集成用户角色。 [此处](#create-integration-user-role-in-servicenow-app)提供了有关如何创建集成用户角色的信息。

## <a name="connection-procedure"></a>**连接过程**
使用以下过程创建 ServiceNow 连接：


1. 在 Azure 门户中，转到“所有资源”，查找“ServiceDesk(YourWorkspaceName)”

2.  在“工作区数据源”下，单击“ITSM 连接”。
    ![新建连接](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”。

4. 提供下表中所述的信息，并单击“确定”创建连接。


> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入需要与 ITSMC 连接的 ServiceNow 实例的名称。  以后在此 ITSM 中配置工作项/查看详细日志分析时，需要在 Log Analytics 中使用此名称。 |
| **合作伙伴类型**   | 选择“ServiceNow”。 |
| **用户名**   | 键入在 ServiceNow 应用中创建的、用于支持连接到 ITSMC 的集成用户名。 详细信息：[创建 ServiceNow 应用用户角色](#create-integration-user-role-in-servicenow-app)。|
| **密码**   | 键入此用户名的关联密码。 **注意**：用户名和密码仅用于生成身份验证令牌，不会存储在 ITSMC 服务中的任何位置。  |
| **服务器 URL**   | 键入需要连接到 ITSMC 的 ServiceNow 实例的 URL。 该 URL 应指向后缀为“.servicenow.com”的支持的 SaaS 版本。|
| **客户端 ID**   | 键入前面生成的、用于 OAuth2 身份验证的客户端 ID。  有关生成客户端 ID 和机密的详细信息： [OAuth 设置](https://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **数据同步范围**   | 选择要通过 ITSMC 同步到 Azure Log Analytics 的 ServiceNow 工作项。  选定的值将导入到 Log Analytics。   **选项：** “事件”和“更改请求”。|
| **同步数据** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

![ServiceNow 连接](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

成功连接并同步后：

- 选定的工作项将从 ServiceNow 实例导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 ServiceNow 实例中，可以根据 Log Analytics 警告、日志记录或 Azure 警报创建事件。

> [!NOTE]
> 在 ServiceNow 中，每小时的请求会有一个速率限制。 若要配置此限制，请通过定义 ServiceNow 实例中的 "入站 REST API 速率限制" 来使用此限制。

## <a name="create-integration-user-role-in-servicenow-app"></a>在 ServiceNow 应用中创建集成用户角色

使用以下过程：

1. 访问 [ServiceNow 应用商店](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)，并在 ServiceNow 实例中安装 **用于 ServiceNow 和 Microsoft OMS 集成的用户应用**。
   
   >[!NOTE]
   >作为从 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续过渡的一部分，OMS 现在称为 Log Analytics。     
2. 安装后，请访问 ServiceNow 实例的左侧导航栏，搜索并选择 Microsoft OMS 集成器。  
3. 单击“安装清单”。

   如果用户角色尚未创建，状态会显示为“未完成”。

4. 在“创建集成用户”旁边的文本框中，输入可连接到 Azure 中的 ITSMC 的用户的用户名。
5. 输入此用户的密码，并单击“确定”。  

> [!NOTE]
> 使用这些凭据在 Azure 中建立 ServiceNow 连接。

将显示新建的用户及其分配的默认角色。

默认角色：
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

成功创建用户后，“检查安装清单”的状态将改为“已完成”，同时会列出针对应用创建的用户角色的详细信息。

> [!NOTE]
> ITSM 连接器无需在 ServiceNow 实例上安装任何其他模块即可向 ServiceNow 发送事件。 若要在 ServiceNow 实例中使用 EventManagement 模块且要使用连接器在 ServiceNow 中创建事件或警报，请将以下角色添加到集成用户：
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>后续步骤

* [ITSM 连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)