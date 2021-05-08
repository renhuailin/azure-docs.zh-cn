---
title: Google 连接器的数据安全和隐私策略
description: 了解 Google 安全和隐私策略对 Azure 逻辑应用中的 Google 连接器（例如 Gmail）的影响
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400751"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure 逻辑应用中 Google 连接器的数据安全和隐私策略

从 2020 年 5 月 1 日起，按照 Google [数据安全和隐私策略](https://www.blog.google/technology/safety-security/project-strobe/)进行的更改可能会影响使用 [Gmail 连接器](/connectors/gmail/)的逻辑应用工作流。 如果你的逻辑应用将 Gmail 连接器与 Gmail 使用者帐户（以 @gmail.com 或 @googlemail.com 结尾的电子邮件地址）一起使用，则你的逻辑应用只能使用特定的[由 Google 批准的触发器、操作和连接器](#approved-connectors)。

> [!NOTE]
> 如果你的逻辑应用将 Gmail 连接器与 G-Suite 业务帐户（带有自定义域的电子邮件地址）一起使用，则你的逻辑应用不会受影响，并且对使用 Gmail 连接器没有任何限制。

## <a name="affected-logic-apps"></a>受影响的逻辑应用

如果你有使用 Gmail 连接器的逻辑应用，则你会收到一封关于可能受影响的逻辑应用的电子邮件。 但是，从 2020 年 6 月 15 日起，任何不符合要求的工作流都会被禁用。 你可以执行下列操作之一：

* [按照此主题中的步骤](#update-affected-workflows)更新受影响的逻辑应用。 你需要创建一个 Google 客户端应用，该应用提供客户端 ID 和客户端机密，你使用它们在 Gmail 触发器或操作中进行身份验证。

* 更新受影响的逻辑应用，使之在你重新启用已禁用的逻辑应用之前仅使用 [Google 批准的连接器](#approved-connectors)。

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google 批准的连接器

根据此策略，当你使用 Gmail 使用者帐户时，你只能将 Gmail 连接器与 Google 批准的特定服务一起使用，这些服务可能会变化。 我们的工程团队持续与 Google 合作，向此列表添加更多服务。 目前，下面是 Google 批准的可以在特定逻辑应用工作流中使用的触发器、操作和连接器，该逻辑应用工作流与你使用 Gmail 使用者帐户时所用 Gmail 连接器所对应的逻辑应用工作流相同：

* 逻辑应用内置触发器和操作：Batch、控制、数据操作、日期时间、平面文件、Liquid、请求、计划、变量和 XML

  Google 未批准的内置触发器和操作（例如 HTTP、Azure Functions、Azure 逻辑应用等）会使逻辑应用不符合 Gmail 连接器的要求，因为该应用可从任何位置发送或接收数据。

* Google 服务：Gmail、Google 日历、Google 联系人、Google Drive、Google 表格和 Google Tasks

* 已批准的 Microsoft 服务：Dynamics 365、Excel Online、Microsoft Teams、Microsoft 365、OneDrive 和 SharePoint Online

* 用于客户管理的数据源的连接器：FTP、RSS、SFTP、SMTP 和 SQL Server

## <a name="non-compliant-examples"></a>不符合要求的示例

下面是一些示例，这些示例将 Gmail 连接器与 Google 未批准的内置触发器、操作或托管连接器一起使用：

* 此逻辑应用将 Gmail 连接器与 HTTP 内置触发器一起使用：

  ![不符合要求的逻辑应用 - 示例 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  此逻辑应用还使用已批准的 Google 日历连接器。

* 此逻辑应用将 Gmail 连接器与 Azure Blob 存储连接器一起使用：

  ![不符合要求的逻辑应用 - 示例 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* 此逻辑应用将 Gmail 连接器与 Twitter 连接器一起使用：

  ![不符合要求的逻辑应用 - 示例 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

有关最新信息，请参阅 [Gmail 连接器技术参考文档](/connectors/gmail/)。

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>适用于受影响逻辑应用的步骤

如果你必须在逻辑应用中将 Gmail 连接器与 Gmail 使用者帐户和 Google 未批准的连接器一起使用，则可以创建你自己的 Google 应用，在企业中供个人或内部使用。 对于此方案，需要执行以下概要步骤：

1. 使用 [Google API 控制台](https://console.developers.google.com)创建一个 Google 客户端应用。

1. 在你的 Gmail 连接器中，使用 Google 客户端应用的客户端 ID 和客户端机密值。

有关详细信息，请参阅 [Gmail 连接器技术参考文档](/connectors/gmail/#authentication-and-bring-your-own-application)。

### <a name="create-google-client-app"></a>创建 Google 客户端应用

若要为客户端应用设置项目，请使用 [Google API 控制台向导](https://console.developers.google.com/start/api?id=gmail&credential=client_key)，按照说明进行操作。 若要了解详细步骤，请查看 [Gmail 连接器技术参考文档](/connectors/gmail/#authentication-and-bring-your-own-application)中的说明。

完成后，屏幕将类似于此示例，但是你有自己的客户端 ID 和客户端机密值。稍后你将在逻辑应用中使用这些值。

![Google 客户端应用的客户端 ID 和客户端机密](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>在逻辑应用中使用客户端应用设置

若要在 Gmail 触发器或操作中使用 Google 客户端应用提供的客户端 ID 和客户端机密，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果你要添加新的 Gmail 触发器或操作并创建全新的连接，请继续执行下一步。 否则，请在 Gmail 触发器或操作中选择“更改连接” > “新增”，例如：

   ![选择“更改连接”>“新增”](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. 提供你的连接信息，例如：

   ![提供连接信息](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | 属性 | 值 | 说明 |
   |----------|-------|-------------|
   | **身份验证类型** | 自带应用程序 | 指定你将使用自己的客户端应用进行身份验证。 |
   | **客户端 ID** | <*client-ID*> | 你的 Google 客户端应用的客户端 ID |
   | **客户端机密** | <*client-secret*> | 你的 Google 客户端应用的客户端机密 |
   ||||

1. 完成后，选择“登录”。

   此时会出现一个页面，其中显示你创建的客户端应用。 如果你使用的是 Gmail 使用者帐户，则可能会出现一个页面，其中显示你的客户端应用未进行 Google 验证，提示你首先允许其访问你的 Google 帐户。

   ![提示访问你的 Google 帐户](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. 如果需要，选择“允许”。

   你现在可以在逻辑应用中使用 Gmail 连接器，没有任何限制。

## <a name="next-steps"></a>后续步骤

详细了解 [Gmail 连接器](/connectors/gmail/)

