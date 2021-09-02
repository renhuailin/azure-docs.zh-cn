---
title: Azure 逻辑应用的连接器概述
description: 了解连接器以及它们如何帮助你快速且轻松地使用 Azure 逻辑应用生成自动化集成工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/01/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: f8db25d79784b1a2ca2b63ace57f729271271a43
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218864"
---
# <a name="about-connectors-in-azure-logic-apps"></a>关于 Azure 逻辑应用中的连接器

使用 Azure 逻辑应用生成工作流时，可以使用连接器来帮助你快速轻松地访问其他应用、服务、系统、协议和平台中的数据、事件和资源，通常情况下无需编写任何代码。 连接器提供预生成的操作，你可以将其用作工作流中的步骤。 Azure 逻辑应用提供数百种可以使用的连接器。 如果没有连接器可用于要访问的资源，可以使用通用 HTTP 操作与服务通信，也可以[创建自定义连接器](#custom-apis-and-connectors)。

本概述介绍了连接器，连接器的常见工作方式，以及 Azure 逻辑应用中更流行和常用的连接器。 有关详细信息，请查看以下文档：

* [Azure 逻辑应用、Microsoft Power Automate 和 Microsoft Power Apps 的连接器概述](/connectors)
* [Azure 逻辑应用的连接器引用](/connectors/connector-reference/connector-reference-logicapps-connectors)
* [Azure 逻辑应用中的定价和计费模型](../logic-apps/logic-apps-pricing.md)
* [Azure 逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="what-are-connectors"></a>什么是连接器？

从技术上讲，连接器是一个代理或封装 API 的包装器，基础服务使用它与 Azure 逻辑应用通信。 此连接器提供在工作流中用于执行任务的操作。 操作以“触发器”或“操作”方式提供，可以配置其中的属性 。 例如，一些触发器和操作还要求首先[创建和配置基础层服务或系统的连接](#connection-configuration)，以便可以对用户帐户的访问权限进行身份验证。

### <a name="triggers"></a>触发器

触发器指定用于启动工作流的事件，始终是所有工作流中的第一步。 每个触发器还遵循可控制触发器如何监视和响应事件的特定触发模式。 通常，触发器遵循轮询模式或推送模式，但有时在两个版本中都可用 。

- 轮询触发器按指定计划定期检查特定服务或系统，查看是否有新数据或特定事件。 如果有新数据可用或发生特定事件，这些触发器将创建并运行工作流的新实例。 然后，这个新实例再使用这些作为输入传递的数据。
- 推送触发器侦听新数据或即将发生的事件，而非轮询。 有新数据可用或事件发生时，这些触发器将创建并运行工作流的新实例。 然后，这个新实例再使用这些作为输入传递的数据。

例如，你可能想要生成一个在文件上传到 FTP 服务器时执行操作的工作流。 作为工作流的第一步，可使用名为“添加或修改文件时”的 FTP 触发器，它遵循轮询模式。 然后，可指定一个计划来定期检查上传事件。

触发器还会将任何输入和其他所需数据传递给工作流，而随后的操作可以在整个工作流中引用和使用该数据。 例如，假设要使用名为“收到新电子邮件时”的 Office 365 Outlook 触发器在收到新电子邮件时启动工作流， 你可将此触发器配置为传递来自每封新电子邮件的内容，例如发件人、主题行、正文、附件等。 然后，工作流可使用其他操作处理该信息。

### <a name="actions"></a>操作

操作是紧跟在触发器之后的操作，会在工作流中执行某种类型的任务。 可以在工作流中使用多个操作。 例如，可使用检测 SQL 数据库中新客户数据的 SQL 触发器启动工作流。 触发器触发后，工作流有一个可获取客户数据的 SQL 操作。 执行 SQL 操作后，工作流还有一个处理数据的操作（不一定是 SQL）。

## <a name="connector-categories"></a>连接器类别

在逻辑应用中，大多数触发器和操作都可在内置版本或托管连接器版本中使用 。 这两个版本都提供少量的触发器和操作。 可用版本取决于你创建的是多租户逻辑应用还是单租户逻辑应用，单租户逻辑应用当前仅在[单租户 Azure 逻辑应用](../logic-apps/single-tenant-overview-compare.md)中可用。

[内置触发器和操作](built-in.md)在逻辑应用运行时中以本机方式运行（无需创建连接），并执行以下类型的任务：

- [在工作流中运行代码](built-in.md#run-code-from-workflows)。
- [组织并控制数据](built-in.md#control-workflow)。
- [管理或处理数据](built-in.md#manage-or-manipulate-data)。

[托管连接器](managed.md)由 Microsoft 部署、托管和管理。 这些连接器提供用于云服务和/或本机系统的触发器和操作。 提供的托管连接器有以下类别：

- [本地连接器](managed.md#on-premises-connectors)，可帮助你访问本地系统中的数据和资源。
- [企业连接器](managed.md#enterprise-connectors)，可提供对企业系统的访问。
- [集成帐户连接器](managed.md#integration-account-connectors)，支持企业对企业 (B2B) 通信方案。
- [集成服务环境 (ISE) 连接器](managed.md#ise-connectors)，这是一小组[仅适用于 ISE 的托管连接器](#ise-and-connectors)。

## <a name="connection-configuration"></a>连接配置

大多数连接器都要求先创建指向目标服务或系统的连接，然后才能在工作流中使用它的触发器或操作。 若要创建连接，必须使用帐户凭据（有时是其他连接信息）对标识进行身份验证。 例如，必须先为指向该帐户的连接授权，然后工作流才可访问和使用 Office 365 Outlook 电子邮件帐户。

### <a name="connection-security-and-encryption"></a>连接安全和加密

对于使用 Azure Active Directory (Azure AD) OAuth 的连接器（例如 Office 365、Salesforce 或 GitHub），必须登录其访问令牌[已加密](../security/fundamentals/encryption-overview.md)并安全存储在 Azure 机密中的服务。 其他连接器（例如 FTP 和 SQL）需要提供包含服务器地址、用户名和密码等配置详细信息的连接。 这些连接配置详细信息同样也会[进行加密并安全存储在 Azure 中](../security/fundamentals/encryption-overview.md)。

只要目标服务或系统允许，建立的连接就可以访问该服务或系统。 对于使用 Azure AD OAuth 连接的服务（例如 Office 365 和 Dynamics），逻辑应用服务会无限期地刷新访问令牌。 其他服务可能会限制在不刷新令牌的情况下，逻辑应用能够使用该令牌的时长。 某些操作（例如更改密码）会使所有访问令牌失效。

尽管是从工作流中创建连接，但连接是单独的 Azure 资源，具有其自己的资源定义。 若要查看这些连接资源定义，请[将逻辑应用从 Azure 下载到 Visual Studio 中](../logic-apps/manage-logic-apps-with-visual-studio.md)。 要创建基本可以部署的有效参数化逻辑应用模板，这是最简单的方法。

> [!TIP]
> 如果组织不允许通过逻辑应用连接器连接到特定资源，可使用 [Azure Policy](../governance/policy/overview.md) [阻止用于创建这些连接的功能](../logic-apps/block-connections-connectors.md)。

<a name="firewall-access"></a>

### <a name="firewall-access-for-connections"></a>连接的防火墙访问

如果使用限制流量的防火墙，而逻辑应用工作流需要通过该防火墙进行通信，需要将防火墙设置为允许访问该逻辑应用工作流所在的 Azure 区域中的逻辑应用服务或运行时使用的[入站](../logic-apps/logic-apps-limits-and-config.md#inbound)和[出站](../logic-apps/logic-apps-limits-and-config.md#outbound) IP 地址。 如果工作流还使用托管连接器（例如 Office 365 Outlook 连接器或 SQL 连接器），或使用自定义连接器，则防火墙还需要允许访问该逻辑应用所在的 Azure 区域中的所有[托管连接器出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)。 有关详细信息，请查看[防火墙配置](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)。

## <a name="recurrence-behavior"></a>定期触发行为

定期内置触发器（如[“定期”触发器](connectors-native-recurrence.md)）在逻辑应用运行时上以本机方式运行，它不同于基于连接的定期触发器，例如需要先创建连接的 Office 365 Outlook 连接器触发器。

对于这两种类型的触发器，如果定期触发未指定具体的开始日期和时间，则在保存或部署逻辑应用时，会立即运行第一次定期触发，而不管触发器的定期设置如何。 若要避免此行为，请提供你希望运行第一次定期触发的开始日期和时间。

### <a name="recurrence-for-built-in-triggers"></a>内置触发器的定期触发

定期内置触发器遵循所设置的计划，包括指定的任何时区。 但如果某个定期触发未指定任何其他高级计划选项（例如具体时间）来运行将来的定期触发，则将来的定期触发会遵照上一次的触发器执行。 因此，这些定期触发的开始时间可能会因存储调用期间的延迟等因素而发生偏移。

有关详细信息，请查看以下文档：

* [使用 Azure 逻辑应用计划和运行重复性自动任务、过程与工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [使用“定期”触发器创建、计划和运行重复任务和工作流](connectors-native-recurrence.md)
* [排查定期触发问题](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>基于连接的触发器的定期触发

在基于连接的定期触发器（如 Office 365 Outlook）中，计划不是控制执行的唯一驱动因素。 时区只能确定最初开始时间。 后续运行取决于定期计划、上一次触发器执行以及其他可能导致运行时间发生偏差或产生意外行为的因素，例如：

* 触发器是否访问具有更多数据的服务器（触发器会立即尝试获取这些数据）。
* 触发器引发的任何故障或重试。
* 调用存储期间的延迟。
* 夏令时 (DST) 开始和结束时，未维护指定的计划。
* 可能影响下次运行时的出现时间的其他因素。

有关详细信息，请查看以下文档：

* [使用 Azure 逻辑应用计划和运行重复性自动任务、过程与工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [排查定期触发问题](#recurrence-issues)

<a name="recurrence-issues"></a>

### <a name="troubleshooting-recurrence-issues"></a>排查定期触发问题

为了确保工作流在你指定的开始时间运行并且不会错过定期触发（特别是在频率为几天或更长时间一次的情况下），请尝试以下解决方案：

* DST 生效时，请手动调整重复周期，使工作流继续按预期时间运行。 否则，开始时间将在 DST 开始时向前移动 1 小时，在 DST 结束时向后移动 1 小时。 有关详细信息和示例，请查看[夏令时和标准时间重复](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)。

* 若要使用“定期”触发器，请指定时区、开始日期和时间。 此外，还应在属性“在这些小时”和“在这些分钟”中（仅适用于“天”和“周”频率）指定运行后续定期触发的具体时间   。 但是，当时间发生调整时，有时 Windows 可能仍会导致问题。

* 请考虑使用[滑动窗口触发器](connectors-native-sliding-window.md)而不是定期触发器，以避免错过定期触发 。

## <a name="custom-apis-and-connectors"></a>自定义 API 和连接器

若要调用运行自定义代码或者无法作为连接器使用的 API，可以通过[创建自定义 API 应用](../logic-apps/logic-apps-create-api-app.md)来扩展逻辑应用平台。 还可以针对任何基于 REST 或 SOAP 的 API [创建自定义连接器](../logic-apps/custom-connector-overview.md)，使这些 API 可供 Azure 订阅中的任何逻辑应用使用。 若要使自定义 API 应用或连接器可供任何人在 Azure 中使用，可以[提交连接器进行 Microsoft 认证](/connectors/custom-connectors/submit-certification)。

## <a name="ise-and-connectors"></a>ISE 和连接器

对于需要直接访问 Azure 虚拟网络中的资源的工作流，你可以创建专用的[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，在该环境中可以针对专用资源生成、部署和运行工作流。 若要详细了解如何创建 ISE，请查看[从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

在 ISE 中创建的自定义连接器无法与本地数据网关一起使用。 但是，这些连接器可以直接访问连接到托管 ISE 的 Azure 虚拟网络的本地数据源。 因此，与这些资源进行通信时，ISE 中的逻辑应用很可能不需要数据网关。 如果在需要本地数据网关的 ISE 外部创建了自定义连接器，ISE 中的逻辑应用就可以使用这些连接器。

在逻辑应用设计器中，当你浏览内置触发器和操作，或要用于 ISE 中的逻辑应用的托管连接器时，内置触发器和操作上会显示“CORE”标签，而专用于 ISE 的托管连接器上会显示“ISE”标签。 

:::row:::
    :::column:::
        ![CORE 连接器示例](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        具有此标签的内置触发器和操作与逻辑应用在同一 ISE 中运行。
    :::column-end:::
    :::column:::
        ![ISE 连接器示例](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        具有此标签的托管连接器与逻辑应用在同一 ISE 中运行。 
        \
        \
        如果你的本地系统已连接到 Azure 虚拟网络，ISE 将允许工作流直接访问该系统，而无需使用[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)。 你可以改为使用该系统的 ISE 连接器（如果有）、HTTP 操作或[自定义连接器](#custom-apis-and-connectors)。
        \
        \
        对于没有 ISE 连接器的本地系统，请使用本地数据网关。 若要查找可用的 ISE 连接器，请查看 [ISE 连接器](#ise-and-connectors)。
    :::column-end:::
    :::column:::
        ![非 ISE 连接器示例](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        无标签     \
        \
        你可以继续使用的所有其他不带标签的连接器都可在全局多租户逻辑应用服务中运行。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>已知问题

下表包含逻辑应用连接器的已知问题。

| 错误消息| 说明 | 解决方法 |
|--------------|-------------|------------|
| `Error: BadGateway. Client request id: '{GUID}'` | 当一个或多个连接不支持 Azure Active Directory (Azure AD) OAuth 身份验证（如 SFTP 和 SQL）时，更新逻辑应用上的标记会生成此错误。 | 若要防止此行为，请避免更新这些标记。 |
||||

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建可从逻辑应用调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)
