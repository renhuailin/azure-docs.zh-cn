---
title: 在 Power BI 中嵌入播放器小部件 - Azure 视频分析器
description: 可以使用 Azure 视频分析器进行连续视频录制或基于事件的录制。 本文介绍了如何在 Microsoft Power BI中嵌入视频，以便为用户提供可自定义的 UI。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: cbb40db29f9f2257d058268e6c639d2a6b89822e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182075"
---
# <a name="embed-player-widget-in-power-bi"></a>在 Power BI 中嵌入播放器小组件

Azure 视频分析器能够将视频和关联的推理元数据[记录](detect-motion-record-video-clips-cloud.md)到视频分析器云资源。 Video Analyzer 有一个[播放器小组件](player-widget.md) - 一个易于嵌入的小部件，允许客户端应用程序播放视频和推理元数据。

通过仪表板可以很好地洞察你的业务，并一目了然地查看所有最重要的指标。 Power BI 仪表板是一种强大的工具，可将视频与多种数据源（包括来自 IoT 中心的遥测数据）相结合。 在本教程中，你将学习如何使用 [Microsoft Power BI](https://powerbi.microsoft.com/) Web 服务将一个或多个播放器小组件添加到仪表板。

## <a name="suggested-pre-reading"></a>建议的读前准备

- Azure 视频分析器[播放器小组件](player-widget.md)
- [Power BI 仪表板](/power-bi/create-reports/service-dashboards)简介

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 完成[检测运动并录制视频](detect-motion-record-video-clips-cloud.md)或[连续视频录制](continuous-video-recording.md) - 需要带有视频接收器的管道。

  > [!NOTE] 
  > 视频分析器帐户应至少录制一个视频才能继续。 通过登录 Azure 视频分析器帐户并选择“视频”>“视频分析器”部分来检查视频列表。

- 一个 [Power BI](https://powerbi.microsoft.com/) 帐户。

## <a name="create-a-token"></a>创建令牌

1. 执行[创建令牌](player-widget.md#create-a-token)的步骤。
2. 务必要保持针对颁发者、受众、密钥类型、算法、密钥 ID、RSA 密钥模数、RSA 密钥指数、令牌生成的值。 创建以下访问策略时，将需要这些值。

## <a name="get-embed-code-for-player-widget"></a>获取播放器小组件嵌入代码

1. 使用凭据登录到 [Azure 门户](https://portal.azure.com/)。 找到用于完成先决条件的视频分析器帐户并打开视频分析器帐户窗格。
2. 执行[创建访问策略](player-widget.md#create-an-access-policy)的步骤。
3. 在“视频分析器”部分中选择“视频” 。
4. 从列表中选择任何视频。
5. 单击“小组件设置”。 右侧将打开“在应用程序中使用小组件”窗格。 向下滚动到“选项 2 – 使用 HTML”，复制代码并将其粘贴到文本编辑器中。 单击“关闭”按钮。

   :::image type="content" source="./media/power-bi/widget-code.png" alt-text="从 AVA 门户复制小组件 HTML 代码并保存以备后用的屏幕截图。":::

6. 编辑在第 5 步中复制的 HTML 代码以替换值
   - 令牌 AVA-API-JWT-TOKEN - 替换为在“创建令牌”步骤中保存的令牌值。 确保删除尖括号。
   - 可选 - =可以在此代码中进行其他 UI 更改，例如 - 将标题从“示例播放器小部件”更改为“连续视频录制”。

## <a name="add-widget-in-power-bi-dashboard"></a>在 Power BI 仪表板中添加小组件

1. 在浏览器中打开 [Power BI 服务](http://app.powerbi.com/)。 从导航窗格中，选择“我的工作区”

   :::image type="content" source="./media/power-bi/power-bi-workspace.png" alt-text="Power BI 工作区主页的屏幕截图。":::

2. 通过单击“新建” > “仪表板”或打开现有仪表板来创建新仪表板。 选择“编辑”下拉箭头，然后选择“添加磁贴”。 选择“Web 内容” > “下一步”。
3. 在“添加 Web 内容磁贴”中，输入在上一节中的嵌入代码。 单击“应用”。

   :::image type="content" source="./media/power-bi/embed-code.png" alt-text="在 Power BI 仪表板磁贴中嵌入 html 代码的屏幕截图。":::

4. 你将看到一个播放器小组件固定到包含视频的仪表板。

   :::image type="content" source="./media/power-bi/one-player-added.png" alt-text="添加了一个视频播放器小组件的屏幕截图。":::

5. 若要从 Azure 视频分析器视频部分添加更多视频，请按照本部分中的相同步骤操作。

> [!NOTE] 
> 若要从同一视频分析器帐户添加多个视频，一组访问策略和令牌就足够了。

下面是固定到单个仪表板的多个视频 Power BI 示例。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/two-players-added.png" alt-text="作为示例添加的两个视频播放器小组件的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- 详细了解[小组件 API](https://github.com/Azure/video-analyzer/tree/main/widgets)