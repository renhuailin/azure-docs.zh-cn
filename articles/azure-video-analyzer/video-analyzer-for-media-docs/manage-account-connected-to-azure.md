---
title: 管理 Azure 视频分析器媒体版（以前称为视频索引器）帐户
titleSuffix: Azure Video Analyzer for Media
description: 了解如何管理连接到 Azure 的 Azure 视频分析器媒体版（以前称为视频索引器）帐户。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 836cff6a8b55378089a6215b9d0c25e3fcac76c4
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305168"
---
# <a name="manage-a-video-analyzer-for-media-account-connected-to-azure"></a>管理连接至 Azure 的视频分析器媒体版帐户

本文演示如何管理已连接到 Azure 订阅和 Azure 媒体服务帐户的 Azure 视频分析器媒体版（以前称为视频索引器）帐户。

> [!NOTE]
> 只有视频分析器媒体版帐户所有者才能执行本主题中所述的帐户配置调整。

## <a name="prerequisites"></a>先决条件

根据[连接到 Azure](connect-to-azure.md) 中所述，将自己的视频分析器媒体版帐户连接到 Azure。

请务必遵循[先决条件](connect-to-azure.md#prerequisites-for-connecting-to-azure)，并查看上述文章中的[注意事项](connect-to-azure.md#azure-media-services-considerations)。

## <a name="examine-account-settings"></a>帐户设置说明

本部分介绍视频分析器媒体版帐户的设置。

查看设置：

1. 单击右上角的用户图标，并选择“设置”。

    ![视频分析器媒体版中的设置](./media/manage-account-connected-to-azure/select-settings.png)

2. 在“设置”页上，选择“帐户”选项卡。

如果视频索引器帐户已连接到 Azure，则会看到以下信息：

* 基础 Azure 媒体服务帐户的名称。
* 正在运行和已排队的索引作业的数目。
* 已分配的预留单位的数目和类型。

如果帐户需要做出某些调整，“设置”页上会显示与帐户配置相关的错误和警告。 消息中包含链接，单击这些链接会转到 Azure 门户中用于做出更改的确切位置。 有关详细信息，请参阅后面的[错误和警告](#errors-and-warnings)部分。

## <a name="repair-the-connection-to-azure"></a>修复与 Azure 的连接

在[视频分析器媒体版](https://www.videoindexer.ai/)页的“更新到 Azure 媒体服务的连接”对话框中，系统会要求你提供以下设置的值：

|设置|说明|
|---|---|
|Azure 订阅 ID|可以从 Azure 门户中检索订阅 ID。 单击左侧面板中的“所有服务”，然后搜索“订阅”。 选择“订阅”并从订阅列表中选择所需的 ID。|
|Azure 媒体服务资源组名称|资源组（你在其中创建了媒体服务帐户）的名称。|
|应用程序 ID|你为此视频分析器媒体版帐户创建的 Azure AD 应用程序 ID（具有指定媒体服务帐户的权限）。 <br/><br/>若要获取应用 ID，请导航到 Azure 门户。 在“媒体服务帐户”下，选择你的帐户，然后转到“API 访问权限”。 选择“使用服务主体连接到媒体服务 API” -> “Azure AD 应用”。 复制相关参数。|
|应用程序密钥|与上面指定的媒体服务帐户关联的 Azure AD 应用程序密钥。 <br/><br/>若要获取应用密钥，请导航到 Azure 门户。 在“媒体服务帐户”下，选择你的帐户，然后转到“API 访问权限”。 选择“使用服务主体连接到媒体服务 API” -> “管理应用程序” -> “证书和机密”。 复制相关参数。|

## <a name="errors-and-warnings"></a>错误和警告

如果帐户需要做出某些调整，“设置”页上会显示与帐户配置相关的错误和警告。 消息中包含链接，单击这些链接会转到 Azure 门户中用于做出更改的确切位置。 本部分提供有关错误和警告消息的更多详细信息。

* EventGrid

    必须使用 Azure 门户注册事件网格资源提供程序。 在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”>“[订阅]”>“ResourceProviders” > “Microsoft.EventGrid”。   如果该提供程序不是处于“已注册”状态，请选择“注册”。  注册需要花费几分钟时间。

* 流式处理终结点

    确保基础媒体服务帐户中包含处于已启动状态的默认 **流式处理终结点**。 否则，无法通过此媒体服务帐户或视频分析器媒体版观看视频。

* 媒体保留单位

    必须在媒体服务资源中分配媒体预留单位才能为视频编制索引。 为获得最佳索引性能，我们建议至少分配 10 个 S3 预留单位。 有关定价信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题解答”部分。

## <a name="next-steps"></a>后续步骤

可以按照[使用 API](video-indexer-use-apis.md) 中的说明，以编程方式来与试用帐户或连接到 Azure 的视频分析器媒体版帐户进行交互。

应使用在连接到 Azure 时所用的同一 Azure AD 用户。
