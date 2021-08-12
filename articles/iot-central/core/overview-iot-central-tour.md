---
title: 浏览 Azure IoT Central UI | Microsoft Docs
description: 熟悉用来创建、管理和使用 IoT 解决方案的 Azure IoT Central UI 的主要区域。
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: cffd8e0347f501e82ca6e62d5c120b232869cbc0
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653314"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>浏览 Azure IoT Central UI

本文介绍了 Azure IoT Central UI。 可以通过 UI 创建、管理和使用 IoT Central 应用程序及其已连接的设备。

## <a name="iot-central-homepage"></a>IoT Central 主页

可在 [IoT Central 主页](https://apps.azureiotcentral.com/)上详细了解有关 IoT Central 的最新消息和可用功能，创建新的应用程序以及查看和启动现有的应用程序。

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central 主页":::

### <a name="create-an-application"></a>创建应用程序

在“生成”部分，你可以浏览与行业相关的 IoT Central 模板列表，也可以使用“自定义应用”模板从头开始。  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central 生成页":::

有关详细信息，请参阅[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

### <a name="launch-your-application"></a>启动应用程序

通过导航到在应用创建过程中选择的 URL 来启动 IoT Central 应用程序。 你还可以在 [IoT Central 应用管理器](https://apps.azureiotcentral.com/myapps)中看到有权访问的所有应用程序的列表。

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central 应用管理器":::

## <a name="navigate-your-application"></a>导航应用程序

进入 IoT 应用程序后，使用左侧窗格访问各种功能。 可选择左窗格顶部的三行图标来展开或折叠该窗格：

> [!NOTE]
> 左窗格中显示的项目将取决于用户角色。 详细了解[管理用户和角色](howto-manage-users-roles.md)。 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="左窗格":::

  :::column-end:::
  :::column span="2":::
     “仪表板”显示所有应用程序和个人仪表板。 
     
     “设备”可用于可以管理所有设备。

     “设备组”允许查看和创建由查询指定的设备的集合。 在应用程序中使用设备组来执行批量操作。

     “规则”可用于创建和编辑用于监视设备的规则。 规则是基于设备数据和触发可自定义操作来评估的。

     “分析”公开了丰富的功能，以便分析历史趋势，并从设备关联不同的遥测。

     “作业”允许通过运行批量操作来大规模管理设备。

     “设备模板”可用于创建和管理连接到应用程序的设备的特性。

     “数据导出”允许配置连续导出到外部服务，例如存储和队列。

     “管理”用于管理应用程序的设置、自定义、计费、用户和角色。

     使用“我的应用”可返回到 IoT Central 应用管理器。
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>搜索、帮助、主题和支持

顶部的菜单显示在每个页面中：

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central 工具栏":::

* 若要搜索设备，请输入“搜索”值。
* 若要更改 UI 语言或主题，请选择“设置”图标。 详细了解[管理应用程序首选项](howto-manage-preferences.md)
* 若要获取帮助和支持，请选择 **帮助** 下拉列表以显示资源列表。 你可从“关于你的应用”链接中[获取有关应用程序的信息](howto-faq.yml#how-do-i-get-information-about-my-application-)。 在针对免费定价计划的应用程序中，支持资源包括对[实时聊天](howto-show-hide-chat.md)的访问权限。
* 若要从应用程序中注销，请选择“帐户”图标。

可以为 UI 选择浅色主题或深色主题：

> [!NOTE]
> 如果管理员已为应用程序配置自定义主题，则无法在浅色主题和深色主题之间切换。

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="IoT Central“选择主题”的屏幕截图。":::

### <a name="dashboard"></a>仪表板

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="IoT Central 仪表板的屏幕截图。":::

* “仪表板”是登录到 IoT Central 应用程序后看到的第一个页面。 可以创建和自定义多个应用程序仪表板。 详细了解[将磁贴添加到仪表板](howto-manage-dashboards.md)

* 还可以创建个人仪表板来监视你关注的内容。 若要了解详细信息，请参阅[创建 Azure IoT Central 个人仪表板](howto-manage-dashboards.md)操作指南文章。

### <a name="devices"></a>设备

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="“设备”页的屏幕截图。":::

此页面显示 IoT Central 应用程序中的设备，按 _设备模板_ 分组。

* 设备模板定义可以连接到应用程序的设备类型。
* 设备表示应用程序中的真实或模拟设备。

### <a name="device-groups"></a>设备组

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="“设备组”页":::

此页面可用于在 IoT Central 应用程序中创建和查看设备组。 可以使用设备组在你的应用程序中执行批量操作或分析数据。 若要了解详细信息，请参阅[在 Azure IoT Central 应用程序中使用设备组](tutorial-use-device-groups.md)一文。

### <a name="rules"></a>规则
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="“规则”页的屏幕截图。":::

通过此页可以基于设备数据查看和创建规则。 当规则触发时，它可以触发一个或多个操作，例如发送电子邮件或调用 webhook。 若要了解信息，请参阅[配置规则](tutorial-create-telemetry-rules.md)教程。

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="“分析”页的屏幕截图。":::

“分析”公开了丰富的功能，以便分析历史趋势，并从设备关联不同的遥测。 若要了解详细信息，请参阅[为 Azure IoT Central 应用程序创建分析](howto-create-analytics.md)一文。

### <a name="jobs"></a>作业

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="“作业”页":::

使用此页可以查看和创建作业，这些作业可用于设备上的批量设备管理操作。 可以更新设备属性、设置，并对设备组执行命令。 若要了解详细信息，请参阅[运行作业](howto-manage-devices-in-bulk.md)文章。

### <a name="device-templates"></a>设备模板

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="设备模板的屏幕截图。":::

在“设备模板”页中，可在应用程序中查看和创建设备模板。 若要了解详细信息，请参阅[在 Azure IoT Central 应用程序中定义新的设备类型](howto-set-up-template.md)教程。

### <a name="data-export"></a>数据导出

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="数据导出":::

借助数据导出，可设置到外部系统的数据流。 若要了解详细信息，请参阅[导出 Azure IoT Central 中的数据](./howto-export-data.md)一文。

### <a name="administration"></a>管理

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="IoT 管理的屏幕截图。":::

借助管理页可配置和自定义 IoT Central 应用程序。 你可在此更改应用程序名称、URL、主题、管理用户和角色、创建 API 令牌以及导出应用程序。 若要了解详细信息，请参阅[管理 Azure IoT Central 应用程序](howto-administer.md)一文。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解了 Azure IoT Central 并且熟悉了 UI 的布局，建议的下一步骤是完成[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。
