---
title: 连接到 IBM MQ 服务器
description: 使用 Azure 逻辑应用从工作流连接到本地或 Azure 中的 MQ 服务器。
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/25/2021
tags: connectors
ms.openlocfilehash: e852f0ce1584a0858f9523c1ea055d4f2cd616d2
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376248"
---
# <a name="connect-to-an-ibm-mq-server-from-a-workflow-in-azure-logic-apps"></a>从 Azure 逻辑应用中的工作流连接到 IBM MQ 服务器

MQ 连接器有助于将逻辑应用工作流连接到本地或 Azure 中的 IBM MQ 服务器。 之后，你便可让工作流接收和发送存储在 MQ 服务器中的消息。 本文通过介绍如何连接到 MQ 服务器和向工作流添加 MQ 操作，提供了 MQ 连接器入门使用指南。 例如，首先可以在队列上浏览一条消息，然后尝试其他操作。

此连接器包含一个通过 TCP/IP 网络与远程 MQ 服务器通信的 Microsoft MQ 客户端。 可以连接到以下 IBM WebSphere MQ 版本：

* MQ 7.5
* MQ 8.0
* MQ 9.0、9.1 和 9.2

<a name="available-operations"></a>

## <a name="available-operations"></a>可用操作

* 多租户 Azure 逻辑应用：创建逻辑应用（使用量）资源时，仅可使用托管 MQ 连接器连接到 MQ 服务器。 此连接器仅提供操作，而不提供触发器。

* 单租户 Azure 逻辑应用：创建基于单租户的逻辑应用工作流时，可以使用托管 MQ 连接器（仅包括操作）或内置 MQ 操作（包括触发器和操作）连接到 MQ 服务器。  

有关托管连接器与内置操作之间的差异的详细信息，请查看[逻辑应用中的关键术语](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

#### <a name="managed"></a>[托管](#tab/managed)

下表仅介绍了可用于 MQ 的部分托管操作：

* 浏览单条消息或一组消息，但不从 MQ 服务器中将其删除。 对于多条消息，可以指定每批返回的最大消息数量。 否则，将返回所有消息。
* 从 MQ 服务器中删除单条或一组消息。
* 接收单条消息或一组消息，然后从 MQ 服务器中将其删除。
* 将单条消息发送到 MQ 服务器。

有关所有托管连接器操作和属性、限制等其他技术信息，请查看 [MQ 连接器的参考页](/connectors/mq/)。

#### <a name="built-in"></a>[内置](#tab/built-in)

下表仅介绍了可用于 MQ 的部分内置操作：

* 当队列中有一条可用消息时，请执行一些操作。
* 当从队列接收一条或多条消息（自动完成）时，请执行一些操作。
* 当从队列接收一条或多条消息（速览锁定）时，请执行一些操作。
* 从队列接收单条消息或一组消息。 对于多条消息，可以指定每批返回的最大消息数量和最大批大小（以 KB 为单位）。
* 将单条消息或一组消息发送到 MQ 服务器。

这些内置 MQ 操作还具有以下功能，另有[单租户逻辑应用服务](../logic-apps/single-tenant-overview-compare.md)中逻辑应用的所有其他功能的优势：

* 用于传输中数据的传输层安全性 (TLS) 加密
* 发送和接收操作的消息编码
* 当逻辑应用使用 Azure Functions 高级计划时，支持 Azure 虚拟网络集成

---

## <a name="limitations"></a>限制

MQ 连接器不使用消息的“Format”字段，也不执行任何字符集转换。 连接器仅将出现在消息字段中的所有数据放入 JSON 消息中，然后将其发送出去。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 如果使用本地 MQ 服务器，则在网络中的服务器上[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 要使 MQ 连接器正常工作，采用本地数据网关的服务器还必须安装 .NET Framework 4.6。

  安装网关后，还必须在 Azure 中创建数据网关资源。 MQ 连接器会使用此资源访问 MQ 服务器。 有关详细信息，请查看[设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。 

  > [!NOTE]
  > 下列情况无需网关：
  > 
  > * 你将使用内置操作，而不使用托管连接器。
  > * MQ 服务器公开可用或在 Azure 中可用。

* 要在其中访问 MQ 服务器的逻辑应用工作流。 逻辑应用资源必须与 Azure 中的网关资源位于同一位置。

  MQ 连接器没有任何触发器，因此工作流必须已经以触发器开头，或者首先必须将触发器添加到工作流。 例如，可以使用[定期触发器](../connectors/connectors-native-recurrence.md)。

  如果不熟悉 Azure 逻辑应用，请尝试此[创建示例逻辑应用工作流的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)（该工作流在多租户逻辑应用服务中运行）。

<a name="create-connection"></a>

## <a name="create-an-mq-connection"></a>创建 MQ 连接 

首次添加 MQ 操作时，系统会提示创建与 MQ 服务器的连接。

> [!NOTE]
> MQ 连接器目前仅支持服务器身份验证，不支持客户端身份验证。 有关详细信息，请参阅[连接和身份验证问题](#connection-problems)。

#### <a name="managed"></a>[托管](#tab/managed)

1. 如果要连接到本地 MQ 服务器，请选择“通过本地数据网关连接”。

1. 提供 MQ 服务器的连接信息。

   | 属性 | 本地或 Azure | 说明 |
   |----------|----------------------|-------------|
   | **网关** | 仅限本地 | 选择“通过本地数据网关连接”。 |
   | **连接名称** | 推送、请求和匿名 | 要为你的连接使用的名称 |
   | **Server** | 推送、请求和匿名 | 使用以下任何一值： <p><p>- MQ 服务器主机名 <br>- IP 地址，后跟冒号和端口号 |
   | 队列管理器名称 | 推送、请求和匿名 | 要使用的队列管理器 |
   | **频道名称** | 推送、请求和匿名 | 连接到队列管理器的通道 |
   | 默认队列名称 | 推送、请求和匿名 | 队列的默认名称 |
   | 连接身份 | 推送、请求和匿名 | 连接到 MQ 服务器的用户名 |
   | **用户名** | 推送、请求和匿名 | 用户名凭据 |
   | **密码** | 推送、请求和匿名 | 密码凭据 |
   | **启用 SSL?** | 仅限本地 | 使用传输层安全性 (TLS) 和安全套接字层 (SSL) |
   | 网关 - 订阅 | 仅限本地 | 与 Azure 中的网关资源关联的 Azure 订阅 |
   | 网关 - 连接网关 | 仅限本地 | 要使用的网关资源 |
   ||||

   例如：

   ![显示托管 MQ 连接详细信息的屏幕截图。](media/connectors-create-api-mq/managed-connection-properties.png)

1. 完成操作后，选择“创建”。

#### <a name="built-in"></a>[内置](#tab/built-in)

1. 提供 MQ 服务器的连接信息。

   | 属性 | 本地或 Azure | 说明 |
   |----------|----------------------|-------------|
   | **连接名称** | 推送、请求和匿名 | 要为你的连接使用的名称 |
   | **服务器名称** | 推送、请求和匿名 | MQ 服务器名称或 IP 地址 |
   | **端口号** | 推送、请求和匿名 | 连接到主机上的队列管理器的 TCP 端口号 |
   | **频道** | 推送、请求和匿名 | 连接到队列管理器的通道 |
   | 队列管理器名称 | 推送、请求和匿名 | 要使用的队列管理器 |
   | 默认队列名称 | 推送、请求和匿名 | 队列的默认名称 |
   | 连接身份 | 推送、请求和匿名 | 连接到 MQ 服务器的用户名 |
   | **用户名** | 推送、请求和匿名 | 用户名凭据 |
   | **密码** | 推送、请求和匿名 | 密码凭据 |
   | 使用 TLS | 推送、请求和匿名 | 使用传输层安全性 (TLS) |
   ||||

   例如：

   ![显示内置 MQ 连接详细信息的屏幕截图。](media/connectors-create-api-mq/built-in-connection-properties.png)

1. 完成操作后，选择“创建”。

---

<a name="add-action"></a>

## <a name="add-an-mq-action"></a>添加 MQ 操作

在 Azure 逻辑应用中，操作会紧跟某个触发器或另一操作，并在工作流中执行某些操作。 以下步骤介绍了添加操作（例如“浏览单条消息”）的一般方法。

1. 在逻辑应用设计器中，打开工作流（如果尚未打开）。

1. 在触发器或另一操作下，添加一个新步骤。

   若要在现有步骤之间添加一个步骤，请将鼠标移到箭头上方。 选择出现的加号 (+)，然后选择“添加操作”。

1. 在操作搜索框中，输入 `mq`。 从“操作”列表中，选择名为“浏览消息”的操作。

1. 如果系统提示创建 MQ 服务器连接，请[提供请求的连接信息](#create-connection)。

1. 在该操作中，提供操作所需的属性值。

   有关更多属性，请打开“添加新参数”列表，然后选择要添加的属性。

1. 完成后，请在设计器工具栏上选择“保存”。

1. 若要测试工作流，请在设计器工具栏中选择“运行”。

   运行完成后，设计器将显示工作流的运行历史记录以及步骤的状态。

1. 若要查看每个已运行（未跳过）步骤的输入和输出，请展开或选择该步骤。

   * 若要查看与输入相关的更多详细信息，请选择“显示原始输入”。
   * 若要查看与输出相关的更多详细信息，请选择“显示原始输出”。 如果将“IncludeInfo”设置为“true”，则会包括更多输出。 

## <a name="troubleshoot-problems"></a>排查问题

### <a name="failures-with-browse-or-receive-actions"></a>浏览或接收操作失败

如果在空队列上运行浏览或接收操作，该操作将失败并出现以下标头输出：

![MQ 的“无消息”错误](media/connectors-create-api-mq/mq-no-message-error.png)

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>连接和身份验证问题

工作流尝试连接到本地 MQ 服务器时，你可能会收到此错误：

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果你直接在 Azure 中使用 MQ 连接器，则 MQ 服务器需要使用一个由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的证书。

* MQ 服务器要求你界定要与 TLS 连接配合使用的密码规范。 但是，出于安全目的，也是为了包含最佳安全套件，Windows 操作系统会发送一组受支持的密码规范。

  MQ 服务器运行所在的操作系统会选择要使用的套件。 若要使配置匹配，必须更改 MQ 服务器设置，以便密码规范与 TLS 协商中所选的选项匹配。

  尝试连接时，MQ 服务器会记录一条事件消息，即连接因 MQ 服务器选择了不正确的密码规范而失败。 这条事件消息包含 MQ 服务器从列表中选择的加密规范。 更新通道配置中的密码规范，以匹配事件消息中的密码规范。

## <a name="connector-reference"></a>连接器参考

有关托管连接器的中所有操作和属性、限制等其他技术信息，请查看 [MQ 连接器的参考页](/connectors/mq/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
