---
title: 用于访问商业市场分析数据的示例应用程序
description: 使用示例应用程序生成自己的商业市场分析应用程序。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed1fb34663a2e424b9d48e7adea422bb26726c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748237"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>用于访问商业市场分析数据的示例应用程序

示例应用程序是使用 C# 和 JAVA 语言创建的，已在 [GitHub](https://github.com/partneranalytics) 中提供。

- [C# 示例应用程序](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA 示例应用程序](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

你可以选择从示例应用程序中找到灵感，并使用任何语言生成自己的应用程序。

示例应用程序实现以下目标：

- 生成 Azure Active Directory (Azure AD) 访问令牌。
- 获取可用数据集。
- 创建用户定义的查询。
- 获取用户定义的查询和系统查询。
- 计划报表。

示例应用程序不涉及针对其他功能调用 API 的方法。 但是，调用其他 API 的过程与前文所述相同。

## <a name="how-to-run-the-application"></a>如何运行应用程序

1. 使用以下命令将存储库克隆到本地系统：

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > 有关更多说明，请参阅 GitHub [存储库](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)中的 `ProgrammaticExportSampleAppISV/README.md` 文件。

1. 若要快速运行该应用，请更新 **appsettings.Development.json** 中的客户端 ID 和客户端机密

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="演示 appsettings.Development.json 文件的代码片段。":::

运行该应用会启动本地 Web 服务器并打开一个页面 (`https://localhost:44365/ProgrammaticExportSampleApp/sample`)。

[![演示“计划报表”页。](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

此页将对本地计算机上运行的 Web 服务器发出 API 调用，而该服务器又会发出实际的编程式访问 API 调用。

## <a name="code-snippets"></a>代码片段

用于执行编程式访问 API 调用的 C# 代码的基本结构如下所示：

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="API 调用的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- [用于访问商业市场分析数据的 API](analytics-available-apis.md)
