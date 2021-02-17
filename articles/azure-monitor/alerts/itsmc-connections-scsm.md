---
title: 将 SCSM 连接 IT 服务管理连接器
description: 本文提供了有关如何在 Azure Monitor 中 IT 服务管理连接器 SCSM (ITSMC) 的信息，以集中监视和管理 ITSM 工作项的信息。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 32cc45f318e6310ca89f3341add7164a0712f416
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608301"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>将 System Center Service Manager 与 IT 服务管理连接器连接

本文提供了有关如何配置 System Center Service Manager 实例与 Log Analytics 中的 IT 服务管理连接器 () ITSMC 之间的连接以集中管理工作项的信息。

以下部分提供有关如何将 System Center Service Manager 产品连接到 Azure 中的 ITSMC 的详细信息。

## <a name="prerequisites"></a>先决条件

请确保满足以下先决条件：

- 已安装 ITSMC。 详细信息：[添加 IT 服务管理连接器解决方案](./itsmc-definition.md)。
- 已部署并配置 Service Manager Web 应用程序（Web 应用）。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Web 应用的信息。
- 已创建并配置混合连接。 详细信息：[配置混合连接](#configure-the-hybrid-connection)。
- 支持的 Service Manager 版本：2012 R2 或 2016。
- 用户角色：[高级操作员](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10))。
- 现在，从 Azure Monitor 发送的警报可以在 System Center Service Manager 事件中创建。

> [!NOTE]
> - ITSM 连接器只能连接到基于云的 ServiceNow 实例。 当前不支持本地 ServiceNow 实例。
> - 为了使用自定义 [模板](./itsmc-definition.md#define-a-template) 作为操作的一部分，SCSM 模板中的参数 "ProjectionType" 应映射到 "servicemanager.incidentmanagement.configuration.xml！ProjectionType "。

## <a name="connection-procedure"></a>连接过程

按照以下过程将 System Center Service Manager 实例连接到 ITSMC：

1. 在 Azure 门户中，转到“所有资源”，查找“ServiceDesk(YourWorkspaceName)”

2. 在“工作区数据源”下，单击“ITSM 连接”。

    ![新建连接](media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. 在右窗格的顶部，单击“添加”。

4. 提供下表中所述的信息，并单击“确定”创建连接。

> [!NOTE]
> 所有这些参数都是必需的。

| **字段** | **说明** |
| --- | --- |
| **连接名称**   | 键入要与 ITSMC 连接的 System Center Service Manager 实例的名称。  以后在此实例中配置工作项/查看详细日志分析时，需要使用此名称。 |
| **合作伙伴类型**   | 选择“System Center Service Manager”。 |
| **服务器 URL**   | 键入 Service Manager Web 应用的 URL。 [此处](#create-and-deploy-service-manager-web-app-service)提供了有关 Service Manager Web 应用的详细信息。
| **客户端 ID**   | 键入（使用自动脚本）生成的、用于对 Web 应用进行身份验证的客户端 ID。 [此处](./itsmc-service-manager-script.md)提供了有关自动化脚本的详细信息。|
| **客户端机密**   | 键入为此 ID 生成的客户端机密。   |
| **同步数据**   | 选择需要通过 ITSMC 同步的 Service Manager 工作项。  这些工作项将导入到 Log Analytics。 **选项：** “事件”、“更改请求”。|
| **数据同步范围** | 键入检索数据的过去天数。 **最大限制**：120 天。 |
| **在 ITSM 解决方案中创建新的配置项** | 如果想要在 ITSM 产品中创建配置项，请选择此选项。 选择此选项后，Log Analytics 会在支持的 ITSM 系统中创建受影响的 CI 作为配置项（如果不存在 CI）。 **默认**：已禁用。 |

![Service Manager 连接](media/itsmc-connections-scsm/service-manager-connection.png)

成功连接并同步后：

- 选定的工作项将从 Service Manager 导入到 Azure **Log Analytics**。 可以在 IT Service Management Connector 磁贴中查看这些工作项的摘要。

- 在此 Service Manager 实例中，可以根据 Log Analytics 警报、日志记录或 Azure 警报创建事件。

了解详细信息：[根据 Azure 警报创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="create-and-deploy-service-manager-web-app-service"></a>创建和部署 Service Manager Web 应用服务

为了将本地 Service Manager 与 Azure 中的 ITSMC 连接，Microsoft 已在 GitHub 中创建一个 Service Manager Web 应用。

若要为 Service Manager 设置 ITSM Web 应用，请执行以下操作：

- **部署 Web 应用** – 部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。 可以使用 Microsoft 提供的[自动化脚本](./itsmc-service-manager-script.md)部署 Web 应用。
- 手动 **配置混合连接** - [配置此连接](#configure-the-hybrid-connection)。

### <a name="deploy-the-web-app"></a>部署 Web 应用
使用自动化[脚本](./itsmc-service-manager-script.md)部署 Web 应用，设置属性，并在 Azure AD 上进行身份验证。

通过提供以下所需详细信息来运行脚本：

- Azure 订阅详细信息
- 资源组名称
- 位置
- Service Manager 服务器详细信息（服务器名称、域、用户名和密码）
- Web 应用的站点名称前缀
- ServiceBus 命名空间。

该脚本将使用指定的名称（以及使该名称保持唯一的其他几个字符串）创建 Web 应用。 它将生成 **Web 应用 URL**、**客户端 ID** 和 **客户端机密**。

保存这些值，因为在与 ITSMC 建立连接时需要使用。

**检查 Web 应用安装**

1. 转到“Azure 门户” > “资源”。 
2. 选择 Web 应用，并单击“设置” > “应用程序设置”。 
3. 确认有关通过脚本部署应用时提供的 Service Manager 实例的信息。

## <a name="configure-the-hybrid-connection"></a>配置混合连接

按照以下过程配置混合连接，将 Service Manager 实例与 Azure 中的 ITSMC 连接。

1. 在“Azure 资源”下面找到 Service Manager Web 应用。
2. 单击“设置” > “网络”。 
3. 在“混合连接”下面，单击“配置混合连接终结点”。 

    ![混合连接网络](media/itsmc-connections-scsm/itsmc-hybrid-connection-networking-and-end-points.png)
4. 在“混合连接”边栏选项卡中，单击“添加混合连接”。 

    ![添加混合连接](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-add.png)

5. 在“添加混合连接”边栏选项卡中，单击“创建新的混合连接”。 

    ![新建混合连接](media/itsmc-connections-scsm/itsmc-create-new-hybrid-connection.png)

6. 键入以下值：

   - **终结点名称**：指定新混合连接的名称。
   - **终结点主机**：Service Manager 管理服务器的 FQDN。
   - **终结点端口**：类型 5724
   - **服务总线命名空间**：使用现有的服务总线命名空间，或新建一个。
   - **位置**：选择位置。
   - **Name**：指定服务总线的名称（如果要创建服务总线）。

     ![混合连接值](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-values.png)
6. 单击“确定”关闭“创建混合连接”边栏选项卡并开始创建混合连接。 

    创建混合连接后，它会显示在边栏选项卡下面。

7. 创建混合连接后，请选择该连接，并单击“添加所选混合连接”。

    ![新建混合连接](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>配置侦听器设置

使用以下过程配置混合连接的侦听器设置。

1. 在“混合连接”边栏选项卡中，单击“下载连接管理器”，并将它安装在运行 System Center Service Manager 实例的计算机上。 

    安装完成后，“开始”菜单下会出现“混合连接管理器 UI”选项。 

2. 单击“混合连接管理器 UI”。系统会提示提供 Azure 凭据。

3. 请使用 Azure 凭据登录，并选择在其中创建了混合连接的订阅。

4. 单击“ **保存**”。

现已成功连接到混合连接。

![混合连接成功](media/itsmc-connections-scsm/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> 创建混合连接后，请通过访问部署的 Service Manager Web 应用来验证和测试该连接。 在确保连接成功后，再尝试连接到 Azure 中的 ITSMC。

以下示例图像显示了成功连接的详细信息：

![混合连接测试](media/itsmc-connections-scsm/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>后续步骤

* [IT 服务管理连接器概述](itsmc-overview.md)
* [根据 Azure 警报日志创建 ITSM 工作项](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)