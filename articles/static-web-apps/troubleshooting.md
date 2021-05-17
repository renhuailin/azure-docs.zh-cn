---
title: 排查 Azure Static Web Apps 故障
description: 跟踪使用 Azure Static Web Apps 时所出现问题的初始步骤。
services: static-web-apps
author: geektrainer
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: chrhar
ms.openlocfilehash: 082c3af694932f4e864b99aa8c2cdc7d1f5fca30
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108210135"
---
# <a name="troubleshooting-deployment-and-runtime-errors"></a>排查部署和运行时错误

本文介绍静态 Web 应用部署问题和其他问题的逐步排查指南。

## <a name="retrieve-deployment-error-messages"></a>检索部署错误消息

Azure Static Web Apps 部署工作流使用可以自动运行以下命令的 Node.js [Oryx 生成过程](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md#build)：

```bash
npm install
npm run build # if specified in package.json
npm run build:azure # if specified in package.json
```

此过程引发的任何错误都将记录在 GitHub 工作流运行中。

1. 导航至静态 Web 应用的 GitHub 存储库。
1. 选择“操作”。

    > [!NOTE]
    > 所有失败的工作流运行都将以红色“X”显示，而非绿色复选标记

1. 选择你希望验证的工作流运行的链接。
1. 选择“生成并部署作业”以打开部署的详细信息
1. 选择“生成并部署”以显示日志

    ![静态 Web 应用的部署日志屏幕截图](./media/troubleshooting/build-deploy-log.png)

1. 查看日志和所有错误消息。

    > [!NOTE]
    > 某些警告错误消息可能会显示为红色，如与“.oryx_prod_node_modules”和“工作区”相关的注释。  这些内容属于常规部署过程。

如果任何包安装失败或引发其他问题，你将在此看到原始错误消息，就像你在本地运行 `npm` 命令一样。

### <a name="confirm-folder-configuration"></a>确认文件夹配置

Azure Static Web Apps 需要知道使用哪些文件夹来托管应用程序。 在工作流结束时，生成过程会确认此配置。 所有错误都将在验证步骤执行期间进行记录。

如果在错误日志中看到以下任何一个错误消息，则表明工作流的文件夹配置有误。

| 错误消息 | 说明 |
| --- | --- |
|应用目录位置“/folder”无效。 无法检测到此目录。 | 验证工作流能否反映存储库结构。 |
| 应用版本无法生成项目文件夹“folder”。 | 确保在工作流中正确配置了 `folder` 属性。 |
| 未指定 Api 目录，或找不到指定目录。 | 不会创建 Azure Functions，因为工作流未定义 `api` 文件夹的值。 |

工作流中指定了三个文件夹位置。 确保这些设置与你的项目和任何在部署之前转换源代码所用的工具匹配。

| 配置设置 | 说明 |
| --- | --- |
| `app_location` | 要部署的源代码的根位置。 此设置通常为 / 或项目的 JavaScript 和 HTML 的位置。 |
| `output_location` | 由捆绑程序中的任何生成过程所创建的文件夹的名称，如 webpack。 此文件夹需要由生成过程创建，并且位于子目录 `app_location` 下 |
| `api_location` |Azure Static Web Apps 托管的 Azure Functions 应用的根位置。 此位置指向项目的所有 Azure Functions 的根文件夹，通常是 api。 |

> [!NOTE]
> 不正确的 `api_location` 配置生成的错误消息可能仍可成功生成，这是 Azure Static Web Apps 无需无服务器代码所致。

## <a name="review-server-errors"></a>查看服务器错误

使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 查找运行时错误消息。 如果尚未创建实例，请参阅[Azure Static Web Apps](monitor.md)。 Application Insights 会记录每个错误生成的完整错误消息和堆栈跟踪。

> [!NOTE]
> 你只能查看在安装 Application Insights 之后生成的错误消息。

1. 在 Azure 门户中，打开与静态 Web 应用关联的资源组。
1. 选择 Application Insights 实例，该实例与静态 Web 应用同名（前提是采用上述步骤创建）。
1. 在“调查”下选择“失败”。
1. 在右侧向下滚动至“深入了解”。
1. 右下角“深入了解”中的一个按钮将会显示最近失败操作的数量。

    ![故障屏幕的屏幕截图](./media/troubleshooting/app-insights-errors.png)

1. 选择“x 项操作”按钮，以打开显示最近失败操作的面板。

    ![操作屏幕的屏幕截图](./media/troubleshooting/app-insights-operations.png)

1. 从列表中选择一个错误进行了解。

    ![错误详细信息屏幕的屏幕截图](./media/troubleshooting/app-insights-details.png)

## <a name="environment-variables"></a>环境变量

许多 Web 应用使用环境变量来存储敏感或环境特定设置。 Azure Static Web Apps 通过“应用程序设置”支持环境变量。

“应用程序设置”是为应用程序设置环境变量的键对/值对。 变量可用于通常使用相同语法访问环境变量的应用程序。

部署时，请仔细检查各个环境变量是否设置为“应用程序设置”。

1. 在 Azure 门户中打开静态 Web 应用。
1. 选择“配置”。 “配置”屏幕将显示所有“应用程序设置”的列表。

    ![静态 Web 应用上“配置”屏幕的屏幕截图](media/troubleshooting/app-settings.png)

遵循以下步骤添加新的变量。

1. 选择 **添加** 。
1. 设置“名称”。
1. 设置“值”。
1. 选择“确定”  。
1. 选择“保存”。
