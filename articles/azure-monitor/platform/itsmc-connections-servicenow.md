---
title: 连接 ServiceNow 与 IT 服务管理连接器
description: 了解如何通过 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 连接 ServiceNow 来集中监视和管理 ITSM 工作项。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3cc38fad6f26bf6f382b4a275638c450c8333a04
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955726"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>连接 ServiceNow 与 IT 服务管理连接器

本文介绍如何在 Log Analytics 中配置 ServiceNow 实例与 IT 服务管理连接器 (ITSMC) 之间的连接，以便你可以集中管理 IT 服务管理 (ITSM) 工作项。

## <a name="prerequisites"></a>必备条件
确保满足以下连接先决条件。

### <a name="itsmc-installation"></a>ITSMC 安装

有关安装 ITSMC 的信息，请参阅 [添加 IT 服务管理连接器解决方案](./itsmc-definition.md#add-it-service-management-connector)。

> [!NOTE]
> ITSMC 仅支持从 ServiceNow (SaaS) 产品/服务的官方软件即服务。 不支持 ServiceNow 的专用部署。

### <a name="oauth-setup"></a>OAuth 设置

ServiceNow 支持的版本包括奥兰多、纽约、马德里、伦敦、Kingston、雅加达、伊斯坦布尔、赫尔辛基和 Geneva。

ServiceNow 管理员必须为其 ServiceNow 实例生成客户端 ID 和客户端密码。 根据需要查看以下信息：

- [为奥兰多设置 OAuth](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为纽约设置 OAuth](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为马德里设置 OAuth](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为伦敦设置 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为金斯顿设置 OAuth](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为雅加达设置 OAuth](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为伊斯坦布尔设置 OAuth](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为赫尔辛基设置 OAuth](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [为日内瓦设置 OAuth](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

在设置 OAuth 的过程中，我们建议执行以下操作：

1. [创建用于客户端以访问实例的终结点](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html)。

1. 更新刷新令牌的生存期：

   1. 在 **ServiceNow** 窗格上，搜索 " **系统 OAuth**"，然后选择 " **应用程序注册表**"。 
   1. 选择已定义的 OAuth 的名称，将 **刷新令牌生存期** 更改为 **7776000 秒** (90 天) 。 
   1. 选择“更新”。 

1. 建立一个内部过程，以确保连接保持活动状态。 刷新令牌生存期预计过期之前的几天，执行以下操作：

   1. [完成 ITSM 连接器配置的手动同步过程](./itsmc-resync-servicenow.md)。

   1. 撤消旧的刷新令牌。 出于安全原因，我们不建议保留旧密钥。 
   
      1. 在 **ServiceNow** 窗格上，搜索 " **系统 OAuth**"，然后选择 " **管理令牌**"。 
      
      1. 根据 OAuth 名称和到期日期从列表中选择旧令牌。

         ![显示 OAuth 令牌列表的屏幕截图。](media/itsmc-connections/snow-system-oauth.png)
      1. 选择 "**撤消访问权限**"  >  。

## <a name="install-the-user-app-and-create-the-user-role"></a>安装用户应用并创建用户角色

使用以下过程来安装 "立即服务用户" 应用，并为其创建集成用户角色。 你将使用这些凭据在 Azure 中建立 ServiceNow 连接。

> [!NOTE]
> ITSMC 仅支持从 ServiceNow 存储下载的适用于 Microsoft Log Analytics 集成的官方用户应用。 ITSMC 不支持在 ServiceNow 端或任何不属于官方 ServiceNow 解决方案的应用程序上执行任何代码引入。 

1. 访问 [servicenow 应用商店](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) ，并在 servicenow 实例中安装 **用于 SERVICENOW 和 Microsoft OMS 集成的用户应用** 。
   
   >[!NOTE]
   >作为 (OMS Azure Monitor) Microsoft Operations Management Suite 的持续转换的一部分，OMS 现在称为 Log Analytics。     
2. 安装完成后，请前往 ServiceNow 实例的左侧导航栏，搜索并选择 " **MICROSOFT OMS 集成商**"。  
3. 选择 **安装核对清单**。

   状态显示为 "  **未完成** "，因为尚未创建用户角色。

4. 在 " **创建集成用户**" 旁边的文本框中，输入可连接到 Azure 中的 ITSMC 的用户的名称。
5. 输入此用户的密码，然后选择 **"确定"**。  

将显示新创建的用户，并分配默认角色：

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

成功创建用户后， **检查安装核对清单** 的状态将移到 " **已完成** "，并列出为应用创建的用户角色的详细信息。

> [!NOTE]
> ITSMC 可以将事件发送到 ServiceNow，而不会在 ServiceNow 实例上安装任何其他模块。 如果你在 ServiceNow 实例中使用 EventManagement 模块，并想要使用连接器在 ServiceNow 中创建事件或警报，请将以下角色添加到集成用户：
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>创建连接
使用以下过程创建 ServiceNow 连接。

> [!NOTE]
> 从 Azure Monitor 发送的警报可以在 ServiceNow 中创建以下元素之一：事件、事件或警报。 

1. 在 Azure 门户中转到 " **所有资源** "，并查找 " **ServiceDesk (YourWorkspaceName)**"。

2. 在 " **工作区数据源**" 下，选择 " **ITSM 连接**"。

   ![显示数据源选择的屏幕截图。](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格的顶部，选择 " **添加**"。

4. 提供下表中所述的信息，然后选择 **"确定"**。

   | 字段 | **说明** |
   | --- | --- |
   | **连接名称**   | 输入要使用 ITSMC 连接的 ServiceNow 实例的名称。 稍后在配置 ITSM 工作项和查看详细分析时 Log Analytics 使用此名称。 |
   | **合作伙伴类型**   | 选择“ServiceNow”。 |
   | **服务器 Url**   | 输入要连接到 ITSMC 的 ServiceNow 实例的 URL。 URL 应指向支持的 SaaS 版本，并使用 *servicenow.com* (例如 https://XXXXX.service-now.com/) 。|
   | **用户名**   | 输入在 ServiceNow 应用中创建的、用于支持连接到 ITSMC 的集成用户名。|
   | **密码**   | 输入与此用户名关联的密码。 **注意**：用户名和密码仅用于生成身份验证令牌。 它们不存储在 ITSMC 服务中的任何位置。  |
   | **客户端 Id**   | 输入要用于之前生成的 OAuth2 authentication 的客户端 ID。 有关生成客户端 ID 和机密的详细信息，请参阅 [设置 OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
   | **客户端机密**   | 输入为此 ID 生成的客户端密码。   |
   | **数据同步范围 (天)** | 输入想要数据来自的过去天数。 限制为120天。 |
   | **要同步的工作项**   | 选择要通过 ITSMC 同步到 Azure Log Analytics 的 ServiceNow 工作项。 所选的值将导入 Log Analytics。 选项包括事件和更改请求。|
   | **在 ITSM 产品中创建新配置项目** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择该选项时，如果支持的 ITSM 系统中不存在)  (，则 ITSMC 会创建配置项目。 此项默认禁用。 |

![用于添加 ServiceNow 连接的框和选项的屏幕截图。](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

成功连接并同步后：

- 从 ServiceNow 实例中选择的工作项将导入 Log Analytics。 可以在 " **IT 服务管理连接器** " 磁贴中查看这些工作项的摘要。

- 你可以从 Log Analytics 警报或日志记录，或从此 ServiceNow 实例中的 Azure 警报创建事件。

> [!NOTE]
> ServiceNow 具有每小时请求数的速率限制。 若要配置限制，请在 ServiceNow 实例中定义 **入站 REST API 速率限制** 。

## <a name="next-steps"></a>后续步骤

* [ITSM 连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [解决 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)