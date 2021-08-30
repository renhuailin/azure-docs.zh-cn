---
title: 连接到 SharePoint
description: 使用 Azure 逻辑应用在 SharePoint Online 或本地 SharePoint Server 中监视和管理资源
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 08/11/2021
tags: connectors
ms.openlocfilehash: e53fca8b0e9f8dbca3dba8bd684e71ef25c88738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722526"
---
# <a name="connect-to-sharepoint-resources-using-azure-logic-apps"></a>使用 Azure 逻辑应用连接到 SharePoint 资源

如需将用于在 SharePoint Online 或本地 SharePoint Server 中监视和管理文件、文件夹、列表和项等资源的任务自动化，你可以使用 Azure 逻辑应用和 SharePoint 连接器创建自动化的任务和工作流。

以下列表介绍了可实现自动化的示例任务：

* 创建、更改或删除文件或项时进行监视。
* 创建、获取、更新或删除项。
* 添加、获取或删除附件。 获取附件中的内容。
* 创建、复制、更新或删除文件。 
* 更新文件属性。 获取文件的内容、元数据或属性。
* 列出或提取文件夹。
* 获取列表或列表视图。
* 设置内容审批状态。
* 解析人员。
* 将 HTTP 请求发送到 SharePoint。
* 获取实体值。

在逻辑应用工作流中，可以使用触发器来监视 SharePoint 中的事件，并使输出可为其他操作所用。 可以使用操作针在 SharePoint 中执行各种任务。 还可以让其他操作使用来自 SharePoint 操作的输出。 例如，如果你定期从 SharePoint 检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件警报。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。 或者，尝试此[创建第一个示例逻辑应用工作流的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>先决条件

* 你的 Microsoft Office 365 帐户凭据，用于在使用[工作或学校帐户](https://support.microsoft.com/office/what-account-to-use-with-office-and-you-need-one-914e6610-2763-47ac-ab36-602a81068235#bkmk_msavsworkschool)登录时与 SharePoint 配合使用。

  需要提供这些凭据才能授权工作流访问 SharePoint 帐户。

  > [!NOTE]
  > 如果使用的是[世纪互联运营的 Microsoft Azure](https://portal.azure.cn)，则 Azure Active Directory (Azure AD) 身份验证仅适用于世纪互联 (.cn) 运营的 Microsoft Office 365 帐户，而不适用于 .com 帐户。

* 你的 SharePoint 站点地址

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 对于本地 SharePoint Server 连接，需要在本地计算机上和[已在 Azure 中创建的数据网关资源](../logic-apps/logic-apps-gateway-connection.md)上[安装并设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

  然后，可以在从工作流创建 SharePoint Server 连接时选择要使用的网关资源。

* 需要访问 SharePoint 站点或服务器的逻辑应用工作流。

  * 若要使用 SharePoint 触发器启动工作流，需要一个空白逻辑应用工作流。
  * 若要添加 SharePoint 操作，工作流需要拥有触发器。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/sharepoint/)。

## <a name="connect-to-sharepoint"></a>连接到 SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>添加触发器

1. 请从 Azure 门户、Visual Studio Code 或 Visual Studio 在视觉对象设计器中打开逻辑应用工作流（如果尚未打开）。

1. 在设计器的搜索框中输入 `sharepoint` 作为搜索词。 选择 SharePoint 连接器。

1. 在“触发器”列表中选择想要使用的触发器。

1. 当系统提示你登录并创建连接时，请选择以下选项之一：

   * 对于 SharePoint Online，请选择“登录”，然后验证用户凭据。
   * 对于 SharePoint Server，请选择“通过本地数据网关连接”。 提供要使用的网关资源的请求信息、身份验证类型和其他必要的详细信息。

1. 完成操作后，选择“创建”。

   工作流成功创建连接后，所选的触发器将会显示。

1. 提供设置触发器所需的信息并继续构建工作流。

## <a name="add-an-action"></a>添加操作

1. 请从 Azure 门户、Visual Studio Code 或 Visual Studio 在视觉对象设计器中打开逻辑应用工作流（如果尚未打开）。

1. 选择以下选项之一：

   * 如需添加某个操作作为目前的最后一步，请选择“新建步骤”。
   * 若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择加号 (+)，然后选择“添加操作”。 

1. 在“选择操作”下的搜索框中，输入 `sharepoint` 作为搜索词。 选择 SharePoint 连接器。

1. 从“操作”列表中选择想要使用的操作。

1. 当系统提示你登录并创建连接时，请选择以下选项之一：

   * 对于 SharePoint Online，请选择“登录”，然后验证用户凭据。
   * 对于 SharePoint Server，请选择“通过本地数据网关连接”。 提供要使用的网关资源的请求信息、身份验证类型和其他必要的详细信息。

1. 完成操作后，选择“创建”。

   工作流成功创建连接后，所选操作将会显示。

1. 提供设置操作所需信息并继续构建工作流。

## <a name="next-steps"></a>后续步骤

了解其他[逻辑应用连接器](../connectors/apis-list.md)