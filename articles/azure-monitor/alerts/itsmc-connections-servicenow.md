---
title: 通过 IT 服务管理连接器连接 ServiceNow
description: 了解如何通过 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 来连接 ServiceNow，以集中监视和管理 ITSM 工作项。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 441ed0072eee17ee6250bb7de39442ecfd669cf3
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687778"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>通过 IT 服务管理连接器连接 ServiceNow

本文介绍了如何在 Log Analytics 中配置 ServiceNow 实例与 IT 服务管理连接器 (ITSMC) 之间的连接，以便你可以集中管理 IT 服务管理 (ITSM) 工作项。

## <a name="prerequisites"></a>先决条件
确保符合以下针对连接的先决条件。

### <a name="itsmc-installation"></a>ITSMC 安装

有关安装 ITSMC 的信息，请参阅[添加 IT 服务管理连接器解决方案](./itsmc-definition.md#add-it-service-management-connector)。

> [!NOTE]
> ITSMC 仅支持 ServiceNow 提供的官方服务型软件 (SaaS) 产品/服务。 不支持 ServiceNow 的专用部署。

### <a name="oauth-setup"></a>OAuth 设置

ServiceNow 支持的版本包括魁北克、巴黎、奥兰多、纽约、马德里、伦敦、金斯敦、雅加达、伊斯坦布尔、赫尔辛基和日内瓦。

ServiceNow 管理员必须为其 ServiceNow 实例生成客户端 ID 和客户端机密。 请根据需要参阅以下信息：

- [为魁北克设置 OAuth](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为巴黎设置 OAuth](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为奥兰多设置 OAuth](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为纽约设置 OAuth](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为伦敦设置 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)

在设置 OAuth 时，我们建议：

1. [为客户端创建一个终结点以访问实例](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html)。

1. 更新刷新令牌的生存期：

   1. 在“ServiceNow”窗格上，搜索“系统 OAuth”，然后选择“应用程序注册表”。 
   1. 选择已定义的 OAuth 的名称，将“刷新令牌生存期”更改为 7,776,000 秒（90 天）。 
   1. 选择“更新”。 

1. 建立一个内部程序，用于确保连接保持活动状态。 在刷新令牌生存期预计到期之前的几天，执行以下操作：

   1. [完成 ITSM 连接器配置的手动同步过程](./itsmc-resync-servicenow.md)。

   1. 撤销旧的刷新令牌。 出于安全原因，我们不建议保留旧密钥。 
   
      1. 在“ServiceNow”窗格上，搜索“系统 OAuth”，然后选择“管理令牌”。 
      
      1. 根据 OAuth 名称和到期日期从列表中选择旧令牌。

         ![屏幕截图显示了 OAuth 令牌的列表。](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. 选择“撤销访问权限” > “撤销”。 

## <a name="install-the-user-app-and-create-the-user-role"></a>安装用户应用并创建用户角色

使用以下过程来安装 ServiceNow 用户应用，并为其创建集成用户角色。 你将使用这些凭据在 Azure 中建立 ServiceNow 连接。

> [!NOTE]
> ITSMC 仅支持从 ServiceNow 应用商店下载的适用于 Microsoft Log Analytics 集成的官方用户应用。 ITSMC 不支持在 ServiceNow 端进行的任何代码引入，也不支持不属于官方 ServiceNow 解决方案的任何应用程序。 

1. 访问 [ServiceNow 应用商店](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)，并在你的 ServiceNow 实例中安装用于 ServiceNow 和 Microsoft OMS 集成的用户应用。
   
   >[!NOTE]
   >作为从 Microsoft Operations Management Suite (OMS) 到 Azure Monitor 的持续过渡的一部分，OMS 现在称为 Log Analytics。     
2. 安装后，转到 ServiceNow 实例的左侧导航栏，搜索并选择“Microsoft OMS 集成器”。  
3. 选择“安装清单”。

   状态将显示为“未完成”，因为用户角色尚未创建。

4. 在“创建集成用户”旁边的文本框中，输入可连接到 Azure 中的 ITSMC 的用户的用户名。
5. 输入此用户的密码，然后选择“确定”。  

将显示新建的用户及分配的默认角色：

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

成功创建用户后，“检查安装清单”的状态将变为“已完成”，同时会列出为应用创建的用户角色的详细信息。 

> [!NOTE]
> ITSM 无需在 ServiceNow 实例上安装任何其他模块即可向 ServiceNow 发送事故。 若要在 ServiceNow 实例中使用 EventManagement 模块并使用连接器在 ServiceNow 中创建事件或警报，请将以下角色添加到集成用户：
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>创建连接
使用以下过程创建 ServiceNow 连接。

> [!NOTE]
> 从 Azure Monitor 发送的警报可以在 ServiceNow 中创建以下元素之一：事件、事故或警报。 

1. 在 Azure 门户中，转到“所有资源”，查找“ServiceDesk(YourWorkspaceName)”。

2. 在“工作区数据源”下，选择“ITSM 连接”。

   ![屏幕截图显示了数据源的选择。](media/itsmc-overview/add-new-itsm-connection.png)

3. 在右窗格的顶部，选择“添加”。

4. 提供下表中所述的信息，然后选择“确定”。

   | 字段 | **说明** |
   | --- | --- |
   | **连接名称**   | 输入需要与 ITSMC 连接的 ServiceNow 实例的名称。 以后在配置 ITSM 工作项以及查看详细分析时，请在 Log Analytics 中使用此名称。 |
   | 合作伙伴类型   | 选择“ServiceNow”。 |
   | 服务器 URL   | 输入需要连接到 ITSMC 的 ServiceNow 实例的 URL。 该 URL 应指向后缀为“.servicenow.com”的受支持的 SaaS 版本（例如 `https://XXXXX.service-now.com/`）。|
   | **用户名**   | 输入你在 ServiceNow 应用中创建的集成用户名，该用户名支持连接到 ITSMC。|
   | **密码**   | 输入与此用户名关联的密码。 注意：用户名和密码仅用于生成身份验证令牌。 它们不存储在 ITSMC 服务中的任何位置。  |
   | 客户端 ID   | 输入你之前生成的需要用于 OAuth2 身份验证的客户端 ID。 若要详细了解如何生成客户端 ID 和机密，请参阅[设置 OAuth](https://old.wiki/index.php/OAuth_Setup)。 |
   | **客户端机密**   | 输入为此 ID 生成的客户端机密。   |
   | 数据同步范围（以天为单位） | 输入要从中获取数据的已过去天数。 上限是 120 天。 |
   | 要同步的工作项   | 选择要通过 ITSMC 同步到 Azure Log Analytics 的 ServiceNow 工作项。 选定的值将导入到 Log Analytics。 选项有“事故”和“变更请求”。|
   | 在 ITSM 产品中创建新的配置项 | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，ITSMC 将在受支持的 ITSM 系统中创建配置项（如果不存在）。 此项默认禁用。 |

![屏幕截图显示了用于添加 ServiceNow 连接的框和选项。](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

成功连接并同步后：

- 选定的工作项将从 ServiceNow 实例导入到 Log Analytics 中。 可以在“IT 服务管理连接器”磁贴中查看这些工作项的摘要。

- 在此 ServiceNow 实例中，可以根据 Log Analytics 警报、日志记录或 Azure 警报创建事故。

> [!NOTE]
> ServiceNow 对于每小时请求数有速率限制。 若要配置该限制，请在 ServiceNow 实例中定义“入站 REST API 速率限制”。

## <a name="next-steps"></a>后续步骤

* [ITSM 连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
