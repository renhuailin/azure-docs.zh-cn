---
title: 在 Azure 门户中管理逻辑应用
description: 使用 Azure 门户编辑、启用、禁用或删除逻辑应用。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: 4c4a3b1e5d165681e921d2fadeadc5dea9633d41
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162898"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>在 Azure 门户中管理逻辑应用

可以使用 [Azure 门户](https://portal.azure.com)或 [Visual Studio](manage-logic-apps-with-visual-studio.md) 管理逻辑应用。 本文介绍如何在 Azure 门户中编辑、禁用、启用或删除逻辑应用。 如果不熟悉 Azure 逻辑应用，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)？

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个现有的逻辑应用。 若要了解如何在 Azure 门户中创建逻辑应用，请参阅[快速入门：使用 Azure 逻辑应用创建第一个工作流 - Azure 门户](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-and-open-a-logic-app"></a>查找并打开逻辑应用

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在门户搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

1. 从逻辑应用列表中，通过浏览或筛选列表查找逻辑应用。

1. 若要打开逻辑应用，请选择要管理的应用。

## <a name="view-logic-app-properties"></a>查看逻辑应用属性

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用菜单中的“设置”下，选择“属性”。 

1. 在“属性”窗格中，可以查看并复制有关逻辑应用的以下信息：

   * **名称**
   * 资源 ID
   * **资源组**
   * **位置**
   * **类型** 
   * **订阅名称**
   * **订阅 ID**
   * **访问终结点**
   * **运行时传出 IP 地址**
   * **访问终结点 IP 地址**
   * **连接器传出 IP 地址**

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>禁用或启用逻辑应用

若要在下一次满足触发条件时阻止触发器激发，请禁用逻辑应用。 可以在 Azure 门户中启用或禁用[单个逻辑应用](#disable-enable-single-logic-app)，或者一次性启用或禁用[多个逻辑应用](#disable-or-enable-multiple-logic-apps)。 禁用逻辑应用会以下列方式影响工作流实例：

* 逻辑应用服务将继续所有正在进行和挂起的运行，直到它们完成。 根据卷或积压工作 (backlog)，此过程可能需要一些时间才能完成。

* 逻辑应用服务不会创建或运行新的工作流实例。

* 下一次满足触发器的条件时，触发器不会触发。 但是，触发器状态会记住逻辑应用的停止位置。 因此，如果重新激活逻辑应用，触发器将会针对自上次运行以来未处理的所有项触发。

  若要阻止触发器针对自上次运行以来未处理的项触发，请在重新激活逻辑应用之前清除触发器的状态：

  1. 在逻辑应用中，编辑工作流触发器的任何部分。
  1. 保存所做更改。 此步骤会重置触发器的当前状态。
  1. [重新激活逻辑应用](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-a-single-logic-app"></a>禁用或启用单个逻辑应用

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，选择“概览”。

   * 若要禁用逻辑应用，请在“概述”窗格工具栏上选择“禁用”。
   * 若要启用逻辑应用，请在“概述”窗格工具栏上选择“启用”。

     > [!NOTE]
     > 如果逻辑应用已禁用，则只会看到“启用”选项。
     > 如果逻辑应用已启用，则只会看到“禁用”选项。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>禁用或启用多个逻辑应用

1. 在 Azure 门户的主搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

1. 在“逻辑应用”页上，查看逻辑应用的“状态”列 。

1. 在复选框列中，选择要停止或启动的逻辑应用。

   * 若要停止所选的正在运行的逻辑应用，请在“概述”窗格工具栏上选择“禁用/停止”。 确认选择。
   * 若要启动所选的已停止的逻辑应用，请在“概述”窗格工具栏上选择“启用/启动”。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

   > [!TIP]
   > 如果未显示正确的状态，请刷新页面。

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>删除逻辑应用

可以删除单个逻辑应用，或者一次性删除多个逻辑应用。 删除逻辑应用会以下列方式方式影响工作流实例：

* 逻辑应用服务会可能取消任何正在进行和挂起的运行。

  即使使用较大的卷或积压工作 (backlog)，大多数运行在完成或开始之前都将被取消。 但是，取消过程可能需要一些时间才能完成。 同时，在服务执行取消过程中，可能会选取某些运行来执行。

* 逻辑应用服务不会创建或运行新的工作流实例。

* 如果删除工作流，然后重新创建相同的工作流，则重新创建的工作流不会具有与删除的工作流相同的元数据。 必须重新保存任何调用删除工作流的工作流。 这样，调用方就可获取重新创建的工作流的正确信息。 否则，对重新创建的工作流的调用将失败并显示 `Unauthorized` 错误。 此行为也适用于在集成帐户中使用项目的工作流和调用 Azure 函数的工作流。

1. 在 Azure 门户的主搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

1. 从“逻辑应用”列表的复选框列中，选择要删除的单个或多个逻辑应用。 在工具栏中选择“删除”。

1. 确认框出现时，输入 `yes` 并选择“删除”。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理逻辑应用版本

可以使用 Azure 门户来控制逻辑应用的版本。 可以查找逻辑应用的版本历史记录，提升以前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>查找和查看以前的版本

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用菜单中的“开发工具”下，选择“版本”。 

   ![Azure 门户中逻辑应用的屏幕截图，显示“开发工具”下的“版本”页的选择。](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 从列表中选择要管理的逻辑应用的“版本”。 可以在搜索栏中输入“版本”标识符以筛选列表。

1. 在“历史记录版本”页上，将会看到以前版本的只读模式的详细信息。 可以在逻辑应用的“设计器”和“代码视图”模式之间进行选择。 

   ![逻辑应用历史记录版本页的屏幕截图，显示代码视图和设计器视图选项。](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>提升以前的版本

1. 在逻辑应用的版本历史记录中，[找到并选择要提升的版本](#find-version-history)。

1. 在“历史记录版本”页上，选择“提升”。 

   ![逻辑应用的版本历史记录屏幕截图，显示用于升级以前版本的按钮。](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在打开的“逻辑应用设计器”页上，按需编辑所要提升的版本。 可以在“设计器”和“代码视图”模式之间进行切换。  还可以更新“参数”、“模板”和“连接器”。  

   ![逻辑应用设计器的屏幕截图，显示用于升级逻辑应用以前版本的按钮。](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 若要保存任何更新并完成以前版本的提升，请选择“保存”。 （或者，若要取消更改，请选择“放弃”。） 

   再次[查看逻辑应用的版本历史记录](#find-version-history)时，提升的版本会显示在列表顶部，并具有新的标识符。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](monitor-logic-apps.md)
