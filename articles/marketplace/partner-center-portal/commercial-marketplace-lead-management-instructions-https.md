---
title: 使用 HTTPS 终结点进行潜在顾客管理 - Microsoft 商业市场
description: 了解如何使用 Power Automate 和 HTTPS 终结点管理来自 Microsoft AppSource 和 Azure 市场的潜在顾客。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/21/2021
ms.openlocfilehash: 5b0073f0f6ab3ecfc06d581ab7cf70c6bf27896e
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285159"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>使用 HTTPS 终结点管理商业市场潜在顾客

如果在合作伙伴中心内未明确支持你的客户关系管理 (CRM) 系统接收 Microsoft AppSource 和 Azure 市场潜在顾客，则你可以在 [Power Automate](https://powerapps.microsoft.com/automate-processes/) 中使用 HTTPS 终结点处理这些潜在顾客。 使用 HTTPS 终结点时，可以采用电子邮件通知形式向外发送商业市场潜在顾客，也可以将它们写入到 Power Automate 支持的 CRM 系统。

本文介绍如何在 Power Automate 中创建新流，以生成将用于在合作伙伴中心配置潜在顾客的 HTTP POST URL。 它还包括使用 [Postman](https://www.getpostman.com/downloads/) 测试流的步骤。

>[!NOTE]
>这些说明中使用的 Power Automate 连接器需要付费 Power Automate 订阅。 配置此流之前，请确保考虑到这一点。

## <a name="create-a-flow-by-using-power-automate"></a>使用 Power Automate 创建流

1. 打开 [Power Automate](https://flow.microsoft.com/) 网页。 选择“登录”。 如果还没有帐户，请选择“免费注册”以创建一个。

1. 登录，选择“我的流”，然后将“环境”从“Microsoft (默认)”切换到你的 Dataverse (CRM) 环境。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png" alt-text="说明如何登录到“我的流”。":::

1. 在“+ 新建”下，选择“+ 即时云端流”。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/https-my-flows-create-from-blank.png" alt-text="说明如何选择“我的流”，然后选择“即时云端流”。":::

1. 命名你的流，然后在“选择触发此流的方式”下，选择“收到 HTTP 请求时”。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/https-my-flows-pick-request-trigger.png" alt-text="说明如何生成自动化流窗口“跳过”按钮。":::

1. 单击流步骤以将它展开。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png" alt-text="说明如何展开流步骤。":::

1. 使用以下方法之一配置“请求正文 JSON 架构”：

    - 将 JSON 架构复制到“请求正文 JSON 架构”文本框中。
    - 选择“使用示例有效负载生成架构”。 在“输入或粘贴示例 JSON 有效负载”文本框中，粘贴 JSON 示例。 选择“完成”以创建架构。

    **JSON 架构**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    JSON 示例
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>此时在配置中，可以选择连接到 CRM 系统或者配置电子邮件通知。 基于你的选择按照其余说明进行操作。

### <a name="connect-to-a-crm-system"></a>连接到 CRM 系统

1. 选择“+新建步骤”。
1. 在“搜索连接器和操作”框中搜索并选择一个 CRM 系统。 以下屏幕显示了“创建新记录(Microsoft Dataverse)”作为示例。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/create-new-record.png" alt-text="说明如何创建新记录。":::

1. 选择具有“添加新行”操作的“操作”选项卡。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/select-create-new-record.png" alt-text="说明如何选择“创建新记录”。":::

1. 从“表名称”下拉列表中选择“潜在顾客”。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/select-leads.png" alt-text="说明如何选择潜在顾客。":::

1. Power Automate 将显示一个用于提供潜在顾客信息的窗体。 可以通过选择添加动态内容来映射输入请求中的项。 以下屏幕显示了 OfferTitle 作为示例。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png" alt-text="说明如何添加动态内容。":::

1. 映射所需的字段，然后选择“保存”以保存你的流。 HTTP POST URL 已创建，可在“收到 HTTP 请求时”窗口中进行访问。 使用复制控件复制此 URL，该控件位于 HTTP POST URL 右侧。 使用复制控件非常重要，这样便不会错过整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置潜在顾客管理时需要用到它。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png" alt-text="展示“收到 HTTP 请求时”。":::

### <a name="set-up-email-notification"></a>设置电子邮件通知

1. 现在已完成 JSON 架构，请选择“+ 新建步骤”。
1. 在“选择操作”下，选择“操作”。 
1. 在“操作”选项卡上，选择“发送电子邮件(Office 365 Outlook)” 。

    >[!NOTE]
    >如果要使用其他电子邮件提供程序，请进行搜索并改为选择“发送电子邮件通知(邮件)”作为操作。

    ![添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. 在“发送电子邮件”窗口中配置以下必填字段：

   - **收件人**：至少输入一个有效的电子邮件地址，潜在顾客将发送到该地址。
   - **使用者**：Power Automate 会提供用于添加动态内容的选项，例如以下屏幕中显示的 LeadSource。 首先输入字段名称。 然后从弹出窗口中选择动态内容选取列表。 

        >[!NOTE] 
        > 添加字段名称时，可以在每个名称后面加上一个冒号 (:)，然后选择 Enter 以创建新行。 添加了字段名称之后，随后可以从动态选取列表添加每个关联参数。

        ![使用动态内容添加电子邮件操作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **正文**：从动态内容选取列表添加要包含在电子邮件正文中的信息。 例如，使用姓氏、名字、电子邮件和公司。 完成设置电子邮件通知后，该通知类似于以下屏幕中的示例。


       ![电子邮件通知示例](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. 选择“保存”以完成 Flow 设置。 HTTP POST URL 已创建，可在“收到 HTTP 请求时”窗口中进行访问。 使用复制控件复制此 URL，该控件位于 HTTP POST URL 右侧。 使用此控件非常重要，这样便不会错过整个 URL 的任何部分。 保存此 URL，因为在发布门户中配置潜在顾客管理时需要用到它。

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>测试

可以使用 [Postman](https://app.getpostman.com/app/download/win64) 测试配置。 可在线下载适用于 Windows 的 Postman。 

1. 启动 Postman，然后选择“新建” > “请求”以设置测试工具。 

   ![请求设置测试工具](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. 填写“保存请求”窗体，然后保存到所创建的文件夹中。

   ![“保存请求”窗体](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. 从下拉列表中选择“POST”。 

   ![测试我的流](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. 将在 Power Automate 中创建的流中的 HTTP POST URL 粘贴到显示“输入请求 URL”的位置处。

   ![粘贴 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. 返回 [Power Automate](https://flow.microsoft.com/)。 从 Power Automate 菜单栏转到“我的流”，找到为发送潜在顾客而创建的流。 选择流名称旁的省略号以查看更多选项，然后选择“编辑”。


1. 选择右上角的“测试”，选择“我将执行触发器操作”，然后选择“测试”"。 你会在屏幕顶部看到表示测试已开始的指示。

   ![“我将执行触发器操作”选项](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. 返回到 Postman 应用，然后选择“发送”。

   ![“发送”按钮](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. 返回到你的流并检查结果。 如果一切按预期运行，则你会看到一条消息，指示流已成功。

   ![检查结果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 你还应收到一封电子邮件。 检查你的电子邮件邮件箱。

    >[!NOTE] 
    >如果看不到来自测试的电子邮件，请检查垃圾邮件和垃圾文件夹。 在下面的屏幕中，你只会注意到在配置电子邮件通知时添加的字段标签。 如果这是你的产品/服务所生成的实际潜在顾客，则还会在正文和主题行中看到潜在顾客联系人的实际信息。

   ![收到的电子邮件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>将你的产品/服务配置为将潜在顾客发送到 HTTPS 终结点

准备好在发布门户中为你的产品/服务配置潜在顾客管理信息后，执行以下步骤。

1. 登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165290)。

1. 选择你的产品/服务，然后转到“产品/服务设置”选项卡。

1. 在“潜在顾客”部分下，选择“连接” 。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="潜在顾客":::

1. 在“连接详细信息”弹出窗口中，为“潜在顾客目标”选择“HTTPS 终结点”。 将按照前面步骤创建的流中的 HTTP POST URL 粘贴到“HTTPS 终结点 URL”字段中。
    ![连接详细信息联系人电子邮件](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. 在“联系人电子邮件”下，为公司中应在收到新潜在顾客时收到电子邮件通知的人员输入电子邮件地址。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”。

要确保已成功连接到潜在顾客目标，请选择“验证”按钮。 如果成功，则在潜在顾客目标中会出现测试潜在顾客。

>[!NOTE] 
>必须先完成产品/服务的其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。

生成潜在顾客时，Microsoft 会将潜在顾客发送到流中。 潜在顾客会路由到所配置的 CRM 系统或电子邮件地址。
