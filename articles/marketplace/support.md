---
title: 在合作伙伴中心获取对商业市场计划的支持
description: 了解合作伙伴中心内适用于商业市场计划的支持选项，其中包括如何发出支持请求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: 6075027124352746a3adbb8f6937d3787eb22b9d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602601"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>支持合作伙伴中心的商业市场计划

Microsoft 支持各种产品和服务。 找到正确的支持团队对于确保适当且及时的响应十分重要。 请考虑使用以下有助于将查询路由到相应团队的方案：

- 如果你是发布者，并有来自客户的问题，请让你的客户使用 [Azure 门户](https://portal.azure.com/)中的支持链接请求支持。
- 如果你是发布者，并且检测到在 Azure 上运行的应用程序存在安全问题，请参阅 [如何记录安全事件支持票证](/azure/security/fundamentals/event-support-ticket)。 发布者必须第一时间报告 Azure 市场软件和服务产品相关的可疑安全事件，包括安全事件和安全漏洞。
- 如果你是发布者，并且有与你的应用或服务相关的问题，请查看以下支持选项。

## <a name="get-help-or-open-a-support-ticket"></a>获取帮助或打开支持票证

1. 使用你的工作帐户登录。 如果尚未执行此操作，则需要 [创建合作伙伴中心帐户](partner-center-portal/create-account.md)。

1. 在页面右上角的菜单中，选择 " **支持** " 图标。 " **帮助和支持** " 窗格出现在页面的右侧。

1. 若要获得商业应用商店帮助，请选择 " **商用 marketplace**"。

   ![支持下拉菜单](./media/support/commercial-marketplace-support-pane.png)

1. 在 " **问题摘要** " 框中，输入问题的简要说明。

1. 在 " **问题类型** " 框中，执行下列操作之一：

    - **选项 1**：输入关键字，如： Marketplace、Azure 应用、SaaS 产品/服务、帐户管理、潜在客户管理、部署问题、付出或共同销售的产品/服务迁移。 然后从显示的建议列表中选择问题类型。

    - **选项 2**：选择 "**类别**" 列表中的 "**浏览" 主题**，然后选择 "**商用 Marketplace**"。 然后选择适当的 **主题** 和 **子** 主题。

1. 找到所选主题后，请选择 " **查看解决方案**"。

    ![后续步骤](./media/support/next-step.png)

显示以下选项：

- 若要选择不同的主题，请单击 " **选择另一个问题**"。
- 若要帮助解决此问题，请查看推荐的步骤和文档（如果有）。

    ![建议的解决方案](./media/support/recommended-solutions.png)

如果在自助帮助中找不到答案，请选择 " **提供问题详细信息**"。 填写所有必填字段以加速解决过程，然后选择 " **提交**"。

>[!Note]
>如果尚未登录到合作伙伴中心，可能需要先登录，然后才能创建票证。

## <a name="track-your-existing-support-requests"></a>跟踪你的现有支持请求

若要查看打开和关闭的票证，请在左侧导航菜单中，选择 "**商业 Marketplace**  >  **支持**"。

## <a name="record-issue-details-with-a-har-file"></a>使用 HAR 文件记录问题详细信息

若要帮助支持代理解决你的问题，请考虑将 HTTP 存档格式 (HAR) 文件附加到你的支持票证。 HAR 文件是 web 浏览器中的网络请求日志。

> [!WARNING]
> HAR 文件可能会记录有关合作伙伴中心帐户的敏感数据。

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge 和 Google Chrome

使用 **Microsoft Edge** 或 **GOOGLE Chrome** 生成 HAR 文件：

1. 请在遇到此问题的网页上进行。
2. 在窗口的右上角，选择省略号图标，然后选择 "**更多工具**" "  >  **开发人员工具**"。 可以按 F12 键作为快捷方式。
3. 在 "开发人员工具" 窗格中，选择 " **网络** " 选项卡。
4. 选择 " **停止记录网络日志** "，并 **清除** 以删除现有日志。 记录图标将变为灰色。

    ![如何删除 Microsoft Edge 或 Google Chrome 中的现有日志](media/support/chromium-stop-clear-session.png)

5. 选择 " **记录网络日志** " 开始记录。 开始记录时，记录图标将变为红色。

    ![如何在 Microsoft Edge 或 Google Chrome 中开始录制](media/support/chromium-start-session.png)

6. 再现要解决的问题。
7. 重现问题后，选择 " **停止记录网络日志**"。
8. 选择 " **导出 HAR**"，用向下箭头图标标记，然后保存文件。

    ![如何导出 Microsoft Edge 或 Google Chrome 中的 HAR 文件](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

使用 **Mozilla Firefox** 生成 HAR 文件：

1. 请在遇到此问题的网页上进行。
1. 在窗口的右上角，选择省略号图标，然后选择 " **Web 开发人员**  >  **切换工具**"。 可以按 F12 键作为快捷方式。
1. 选择 " **网络** " 选项卡，然后选择 " **清除** " 以删除现有日志。

    ![如何删除 Mozilla Firefox 中的现有日志](media/support/firefox-clear-session.png)

1. 再现要解决的问题。
1. 重现问题后，选择 " **HAR 导出/导入**" "  >  **全部保存**"。

    ![如何导出 Mozilla Firefox 中的 HAR 文件](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

使用 **Safari** 生成 HAR 文件：

1. 在 safari 中启用开发人员工具：选择 **safari**  >  **首选项**。 中转到 " **高级** " 选项卡，然后选择 **菜单栏中的 "显示开发" 菜单**。
1. 请在遇到此问题的网页上进行。
1. 依次选择“Develop”（开发）、“Show Web Inspector”（显示 Web 检查器）。 
1. 选择 " **网络** " 选项卡，然后选择 " **清除网络项** " 以删除现有日志。

    ![如何在 Safari 中删除现有日志](media/support/safari-clear-session.png)

1. 再现要解决的问题。
1. 重现问题后，选择 " **导出** " 并保存文件。

    ![如何在 Safari 中导出 HAR 文件](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](partner-center-portal/update-existing-offer.md)
