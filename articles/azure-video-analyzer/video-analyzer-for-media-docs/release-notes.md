---
title: Azure 视频分析器媒体版（以前称为视频索引器）发行说明 | Microsoft Docs
description: 为了让大家随时了解最新的开发成果，本文提供了 Azure 视频分析器媒体版（以前称为视频索引器）的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 05/06/2021
ms.author: juliako
ms.openlocfilehash: d67d8dba31a453f4a42982519d619f294d47e04f
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111813812"
---
# <a name="video-analyzer-for-media-release-notes"></a>视频分析器媒体版发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

为了让大家随时了解 Azure 视频分析器媒体版（以前称为视频索引器）的最新开发成果，本文提供了以下信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="may-2021"></a>2021 年 5 月

### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>语音转文本 (STT)、翻译和搜索支持新的源语言

视频分析器媒体版现在支持中文(粤语)（“zh-HK”）、荷兰语(荷兰)（“Nl-NL”）、捷克语（“Cs-CZ”）、波兰语（“Pl-PL”）、瑞典语(瑞典)（“Sv-SE”）、挪威语（“nb-NO”）、芬兰语（“fi-FI”）、加拿大法语（“fr-CA”）、泰语（“th-TH”）、阿拉伯语: (阿拉伯联合酋长国)（“ar-AE”、“ar-EG”）、(伊拉克)（“ar-IQ”）、(约旦)（“ar-JO”）、(科威特)（“ar-KW”）、(黎巴嫩)（“ar-LB”）、(阿曼)（“ar-OM”）、(卡塔尔)（“ar-QA”）、(巴勒斯坦民族权力机构)（“ar-PS”）、(叙利亚)（“ar-SY”）和土耳其语（“tr-TR”）的 STT、翻译与搜索。 

在 API 和视频分析器媒体版网站中都可使用这些语言。 从“视频源语言”下的组合框中选择语言。

### <a name="new-theme-for-azure-video-analyzer-for-media"></a>Azure 视频分析器媒体版的新主题

提供了新主题：“Azure”以及“浅色”和“深色”主题。 若要选择主题，请单击网站右上角的齿轮图标，在“用户设置”下查找主题。
 
### <a name="new-open-source-code-you-can-leverage"></a>可以利用的新开源代码 

[GitHub 存储库](https://github.com/Azure-Samples/media-services-video-indexer)提供三个新的 Git-Hub 项目：

* 可帮助你利用新添加的[小组件自定义](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets)的代码。
* 有助于将[自定义搜索](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/VideoSearchWithAutoMLVision)添加到视频库的解决方案。
* 有助于将[重复数据消除](https://github.com/Azure-Samples/media-services-video-indexer/commit/6b828f598f5bf61ce1b6dbcbea9e8b87ba11c7b1)添加到视频库的解决方案。
 
### <a name="new-option-to-toggle-bounding-boxes-for-observed-people-on-the-player"></a>用于在播放器上切换边界框（用于观测到的人员）的新选项  

当你通过高级视频设置为视频编制索引时，可以查看我们新的观测到的人员功能。

如果在媒体文件中检测到人员，可以通过媒体播放器对检测到的人员启用边界框。

## <a name="april-2021"></a>2021 年 4 月

视频索引器服务已重命名为 Azure 视频分析器媒体版。

### <a name="observed-people-tracing-preview"></a>观测到的人员跟踪（预览版）  

Azure 视频分析器媒体版现在可检测视频中观测到的人员，并提供诸如视频帧中人员的位置以及人员出现时确切的时间戳（开始时间、结束时间）之类的信息。 API 返回每个检测到的人员实例的边界框坐标（以像素为单位），包括其置信度。 

例如，如果视频包含人，则检测操作会在视频帧中列出该人员的外貌及其坐标。 可以使用此功能来确定人员在视频中的路径。 还可以确定视频中是否有多个相同人员的实例。 

通过选择“高级选项” -> “高级视频”或“高级视频 + 音频”预设（在“视频 + 音频索引”下），可在编制文件索引时使用新添加的“观测到的人员跟踪”功能  。 标准和基本索引预设不包括这一全新的高级模型。 

选择在视频分析器媒体版网站上查看视频的见解时，“观测到的人员跟踪”会在页面上显示，其中包含所有观测到的人员缩略图。 可以选择一个人员的缩略图，并查看该人员在视频播放器中的显示位置。  

也可以在视频分析器媒体版生成的 JSON 文件中使用此功能。 有关详细信息，请参阅[跟踪视频中观测到的人员](observed-people-tracing.md)。

### <a name="detected-acoustic-events-with-audio-effects-detection-preview"></a>使用音频效果检测（预览版）检测到声学事件

现在可以在隐藏式字幕文件中查看检测到的声学事件。 可以从视频分析器媒体版门户下载该文件，并且该文件将在 GetArtifact API 中作为项目提供。

音频效果检测（预览版）组件检测各种声学事件，并将它们分类到不同的声学类别（例如枪声、尖叫和人群反应等）。 有关详细信息，请参阅[音频效果检测](audio-effects-detection.md)。

### <a name="improved-upload-experience-in-the-portal"></a>改进了门户中的上传体验

视频分析器媒体版在门户中有新的上传体验：

* Fairfax 中提供了新的开发人员门户

政府云中现在也提供了视频分析器媒体版的新[开发人员门户](https://api-portal.videoindexer.ai)。

## <a name="march-2021"></a>2021 年 3 月

### <a name="audio-analysis"></a>音频分析 

音频分析现在以不同价位在其他新音频功能捆绑包中提供。 新的基本音频分析预设提供一种低成本选项，可以仅提取语音脚本、翻译以及设置输出隐藏式字幕和字幕的格式。 基本音频预设将在帐单上生成两个单独的计量，其中包括用于脚本的行以及用于隐藏式字幕和字幕格式设置的单独行。 有关定价的详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页。

通过选择“高级选项” -> “基本音频”预设（在“视频 + 音频索引”下拉框下），可在编制文件索引或重新编制文件索引时使用新添加的捆绑包。

### <a name="new-developer-portal"></a>新的开发人员门户 

视频分析器媒体版有新的[开发人员门户](https://api-portal.videoindexer.ai/)，你可在其中试用新的视频分析器媒体版 API，并在一个位置查找所有相关资源：[GitHub 存储库](https://github.com/Azure-Samples/media-services-video-indexer)、[Stack Overflow](https://stackoverflow.com/questions/tagged/video-indexer)、[视频分析器媒体版技术社区](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer)（提供相关博客文章）、[视频分析器媒体版常见问题解答](faq.md)、[User Voice](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016)（用于提供你的反馈和功能建议）以及[“CodePen”链接](https://codepen.io/videoindexer)（提供小组件代码示例）。 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>见解小组件的高级自定义功能 

现在可以使用 SDK 在你自己的服务中嵌入视频分析器媒体版的见解小组件，并自定义其样式和数据。 SDK 支持标准的视频分析器媒体版见解小组件和完全可自定义的见解小组件。 [视频分析器媒体版 GitHub 存储库](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization)中提供了代码示例。 借助这些高级自定义功能，解决方案开发人员可以应用自定义样式，引入客户自己的 AI 数据，并在见解小组件中呈现这些数据（包含或不包含视频分析器媒体版见解）。 

### <a name="video-analyzer-for-media-deployed-in-the-us-north-central--us-west-and-canada-central"></a>视频分析器媒体版已部署在美国中北部、美国西部和加拿大中部 

现在，可以在美国中北部、美国西部和加拿大中部区域创建视频分析器媒体版付费帐户
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>语音转文本 (STT)、翻译和搜索支持新的源语言 

视频分析器媒体版现在支持丹麦语（“da-DK”）、挪威语（“nb-NO”）、瑞典语（“sv-SE”）、芬兰语（“fi-FI”）、加拿大法语（“fr-CA”）、泰语（“th-TH”）、阿拉伯语（“ar-BH”、“ar-EG”、“ar-IQ”、“ar-JO”、“ar-KW”、“ar-LB”、“ar-OM”、“ar-QA”、“ar-S”和“ar-SY”）及土耳其语（“tr-TR”）的 STT、翻译与搜索。 在 API 和视频分析器媒体版网站中都可使用这些语言。 
 
### <a name="search-by-topic-in-video-analyzer-for-media-website"></a>在视频分析器媒体版网站中按主题进行搜索 

现在，可以使用[视频分析器媒体版网站](https://www.videoindexer.ai/account/login)页面顶部的搜索功能来搜索包含特定主题的视频。 

## <a name="february-2021"></a>2021 年 2 月

### <a name="multiple-account-owners"></a>多个帐户所有者 

已将帐户所有者角色添加到视频分析器媒体版。 可以添加、更改和删除用户；更改其角色。 有关如何共享帐户的详细信息，请参阅[邀请用户](invite-users.md)。

### <a name="audio-event-detection-public-preview"></a>音频事件检测（公共预览版）

> [!NOTE]
> 仅在试用帐户中提供此功能。 

视频分析器媒体版现在检测内容的非语音部分中的以下音频效果：枪声、玻璃破裂、警报、汽笛、爆炸、犬吠、尖叫、大笑、人群反应（欢呼、拍手和起哄）以及沉默。 

通过选择“高级选项” -> “高级音频”预设（在“视频 + 音频索引”下），可在编制文件索引时使用新添加的音频效果功能。 标准索引将仅包括沉默和人群反应。 

现在，将之前音频效果模型中包含的拍手事件类型作为人群反应事件类型的一部分提取。

选择在[视频分析器媒体版](https://www.videoindexer.ai/)网站上查看视频的见解时，页面上会显示音频效果。

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="音频事件检测":::

### <a name="named-entities-enhancement"></a>命名实体增强功能  

人员和位置提取列表总体上得到扩展和更新。 

此外，该模型现在还包含上下文中不知名的人员和位置，例如视频中的“Sam”或“Home”。 

## <a name="january-2021"></a>2021 年 1 月

### <a name="video-analyzer-for-media-is-deployed-on-us-government-cloud"></a>视频分析器媒体版已部署在美国政府云中 

现在，可以在弗吉尼亚州和亚利桑那州区域的美国政府云中创建视频分析器媒体版付费帐户。 上述区域不提供视频分析器媒体版免费试用版产品/服务。 有关详细信息，请参阅视频分析器媒体版文档。 

### <a name="video-analyzer-for-media-deployed-in-the-india-central-region"></a>视频分析器媒体版已部署在印度中部区域 

现在，可以在印度中部区域创建视频分析器媒体版付费帐户。 

### <a name="new-dark-mode-for-the-video-analyzer-for-media-website-experience"></a>视频分析器媒体版网站体验的全新深色模式

视频分析器媒体版网站体验现在以深色模式提供。 若要启用深色模式，请打开设置面板，然后启用“深色模式”选项。 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="深色模式设置":::

## <a name="december-2020"></a>2020 年 12 月

### <a name="video-analyzer-for-media-deployed-in-the-switzerland-west-and-switzerland-north"></a>视频分析器媒体版已部署在瑞士西部和瑞士北部

现在，可以在瑞士西部和瑞士北部区域创建视频分析器媒体版付费帐户。

## <a name="october-2020"></a>2020 年 10 月

### <a name="animated-character-identification-improvements"></a>动画角色识别改进  

视频分析器媒体版通过与认知服务自定义视觉集成来支持检测、分组和识别动画内容中的角色。 我们在检测和角色识别方面为此 AI 算法添加了重大改进，因此显著提高了见解准确度和识别的角色数。

### <a name="planned-video-analyzer-for-media-website-authenticatication-changes"></a>计划的视频分析器媒体版网站身份验证更改

自 2021 年 3 月 1 日起，你将不再能够使用 Facebook 或 LinkedIn 注册和登录[视频分析器媒体版网站](https://www.videoindexer.ai/)[开发人员门户](video-indexer-use-apis.md)。

你将能够使用以下提供商之一进行注册和登录：Azure AD、Microsoft 和 Google。

> [!NOTE]
> 2021 年 3 月 1 日之后，你将无法访问连接到 LinkedIn 和 Facebook 的视频分析器媒体版帐户。 
> 
> 你应该将自己拥有的 Azure AD、Microsoft 或 Google 电子邮件[邀请](invite-users.md)到视频分析器媒体版帐户，以便仍然可以访问。 可以添加受支持提供商的其他所有者，如[邀请](invite-users.md)中所述。 <br/>
> 或者，可以创建付费帐户并迁移数据。

## <a name="august-2020"></a>2020 年 8 月

### <a name="mobile-design-for-the-video-analyzer-for-media-website"></a>视频分析器媒体版网站的移动设计

视频分析器媒体版网站体验现在支持移动设备。 用户体验可以快速适应你的移动屏幕尺寸（不包括自定义 UI）。 

### <a name="accessibility-improvements-and-bug-fixes"></a>辅助功能改进和 bug 修复 

作为 WCAG（Web 内容辅助功能准则）的一部分，视频分析器媒体版网站体验与 C 级保持一致（Microsoft 辅助功能标准的一部分）。 解决了与键盘导航、编程访问和屏幕阅读器相关的多个 bug 并进行了与此相关的多项改进。 

## <a name="july-2020"></a>2020 年 7 月

### <a name="ga-for-multi-language-identification"></a>用于多语言识别的正式发布版

多语言识别已从预览版变为正式发布版，并可投入生产性使用。

没有与“预览版到正式发布版”过渡相关的价格影响。

### <a name="video-analyzer-for-media-website-improvements"></a>视频分析器媒体版网站改进

#### <a name="adjustments-in-the-video-gallery"></a>视频库中的调整

添加了新的搜索栏，可提供具有附加筛选功能的深度见解搜索。 搜索结果也得到增强。

新的列表视图可以对具有多个文件的视频存档进行排序和管理。

#### <a name="new-panel-for-easy-selection-and-configuration"></a>便于选择和配置的新面板

添加了便于进行选择和用户配置的侧面板，从而实现简单快速的帐户创建和共享以及设置配置。

侧面板还用于用户首选项和帮助。

## <a name="june-2020"></a>2020 年 6 月

### <a name="search-by-topics"></a>按主题搜索

现在，可以使用搜索 API 搜索具有特定主题的视频（仅限 API）。

主题作为 `textScope`（可选参数）的一部分添加。 有关详细信息，请参阅 [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos)。  

### <a name="labels-enhancement"></a>标签增强功能

标签标记器已升级，现在包括更多可以识别的视觉标签。

## <a name="may-2020"></a>2020 年 5 月

### <a name="video-analyzer-for-media-deployed-in-the-east-us"></a>视频分析器媒体版已部署在美国东部

现在，可以在美国东部区域创建视频分析器媒体版付费帐户。
 
### <a name="video-analyzer-for-media-url"></a>视频分析器媒体版 URL

视频分析器媒体版区域终结点均已统一，仅以 www 开头。 不需要任何操作项。

从现在开始，无论是嵌入小组件还是登录视频分析器媒体版 Web 应用程序，都会到达 www.videoindexer.ai。

同时，wus.videoindexer.ai 将重定向到 www。 有关详细信息，请参阅[在应用中嵌入视频分析器媒体版小组件](video-indexer-embed-widgets.md)。

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新的小组件参数功能

见解小组件包括新参数 `language` 和 `control`。

播放机小组件具有新的 `locale` 参数。 `locale` 和 `language` 参数控制播放机的语言。

有关详细信息，请参阅[小组件类型](video-indexer-embed-widgets.md#widget-types)部分。 

### <a name="new-player-skin"></a>新播放机外观

推出了具有更新设计的新播放机外观。

### <a name="prepare-for-upcoming-changes"></a>为即将推出的更改做好准备

* 目前，以下 API 返回 account 对象：

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Account 对象包含指向[视频分析器媒体版网站](https://www.videoindexer.ai/)位置的 `Url` 字段。
对于付费帐户，`Url` 字段目前指向内部 URL，而不是公共网站。
在接下来的几周，我们将对其进行更改，并为所有帐户（试用帐户和付费帐户）返回[视频分析器媒体版网站](https://www.videoindexer.ai/) URL。

    请不要使用内部 URL，而应该使用[视频分析器媒体版公共 API](https://api-portal.videoindexer.ai/)。
* 如果要在应用程序中嵌入视频分析器媒体版 URL，并且这些 URL 不指向[视频分析器媒体版网站](https://www.videoindexer.ai/)或视频分析器媒体版 API 终结点 (`https://api.videoindexer.ai`)，而是指向区域终结点（例如 `https://wus2.videoindexer.ai`），请重新生成 URL。

   为此，可执行以下任一操作：

    * 使用指向视频分析器媒体版小组件 API（例如[见解小组件](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget)）的 URL 替换该 URL
    * 使用视频分析器媒体版网站生成新的嵌入式 URL：
         
         按“播放”进入视频页 -> 单击“&lt;/&gt; 嵌入”按钮 -> 将 URL 复制到应用程序：
   
    区域 URL 不受支持，并将在接下来的几周进行屏蔽。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>针对其他语言的自定义语言支持

视频分析器媒体版现在支持 `ar-SY`、`en-UK` 和 `en-AU` 的自定义语言模型（仅限 API）。
 
### <a name="delete-account-timeframe-action-update"></a>删除帐户操作时间范围更新

现在，删除帐户操作会在 90 天内（而不是 48 小时内）删除帐户。
 
### <a name="new-video-analyzer-for-media-github-repository"></a>新的视频分析器媒体版 GitHub 存储库

现在提供包含不同项目、入门指南和代码示例的新视频分析器媒体版 GitHub： https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger 更新

视频分析器媒体版在单个[视频分析器媒体版 OpenAPI 规范 (swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) 中统一了身份验证和操作 。 开发人员可以在[视频分析器媒体版开发人员门户](https://api-portal.videoindexer.ai/)中找到 API。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>使用新 API 更新脚本

使用 [Update-Video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API 更新脚本中的特定部分。

### <a name="fix-account-configuration-from-the-video-analyzer-for-media-portal"></a>从视频分析器媒体版门户修复帐户配置

现在，可以更新媒体服务连接配置，以自助解决以下问题： 

* 不正确的 Azure 媒体服务资源
* 密码更改
* 媒体服务资源在订阅之间移动  

若要修复帐户配置，请在视频分析器媒体版门户中导航到“设置”>“帐户”选项卡（以所有者身份）。

### <a name="configure-the-custom-vision-account"></a>配置自定义视觉帐户

使用视频分析器媒体版门户在付费帐户上配置自定义视觉帐户（以前，只有 API 支持此功能）。 为此，请登录到视频分析器媒体版门户，选择“模型自定义”>“动画角色”>“配置”。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>场景、镜头和关键帧现在位于一个见解窗格中

场景、镜头和关键帧现已合并为一个见解，以方便使用和导航。 选择所需的场景后，可以看到它包含的镜头和关键帧。 

### <a name="notification-about-a-long-video-name"></a>关于长视频名称的通知

视频名称长度超过 80 个字符时，视频分析器媒体版会在上传时显示描述性错误。

### <a name="streaming-endpoint-is-disabled-notification"></a>“流式处理终结点已禁用”通知

禁用流式处理终结点时，视频分析器媒体版将在播放机页上显示描述性错误。

### <a name="error-handling-improvement"></a>错误处理改进

现在，如果视频主动编制索引，将从[重新编制视频索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)和[更新视频索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API 返回状态代码 409，以防止意外替代当前的重新编制索引更改。

## <a name="november-2019"></a>2019 年 11 月
 
* 韩语自定义语言模型支持

    现在，视频分析器媒体版在 API 和门户中均支持韩语 (`ko-KR`) 自定义语言模型。 
* 语音转文本 (STT) 支持新语言

    视频分析器媒体版 API 现在支持黎凡特阿拉伯语 (ar-SY)、英式英语 (en-GB) 和澳大利亚英语 (en-AU) 的 STT。
    
    对于视频上传，我们将 zh-HANS 替换为 zh-CN，两者均受支持，但建议使用 zh-CN 且其更加准确。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 在库中搜索动画角色

    现在，为动画角色编制索引时，可以在帐户的视频库中搜索它们。 有关详细信息，请参阅[动画角色识别](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
在 IBC 2019 上宣布多项改进：
 
* 动画角色识别（公共预览版）

    通过与自定义视觉集成，可以检测、分组并识别动画内容中的角色。 有关详细信息，请参阅[动画字符检测](animated-characters-recognition.md)。
* 多语言识别（公共预览版）

    检测音轨中包含多种语言的片段，并基于它们创建多语言脚本。 初始支持：英语、西班牙语、德语和法语。 有关详细信息，请参阅[自动识别和转录多语言内容](multi-language-identification-transcription.md)。
* 人员和位置的命名实体提取

    通过自然语言处理 (NLP) 从语音和视觉文本中提取品牌、位置和人员。
* 编辑镜头类型分类

    使用编辑类型标记镜头，例如特写、中景镜头、双人镜头、室内、室外等。有关详细信息，请参阅[编辑镜头类型检测](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主题推理增强功能 - 现在涵盖级别 2
    
    主题推理模型现在支持 IPTC 分类的更深粒度。 在 [Azure 媒体服务的全新 AI 支持的创新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)中阅读完整详细信息。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-analyzer-for-media-deployed-in-uk-south"></a>视频分析器媒体版已部署在英国南部

现在，可以在英国南部区域创建视频分析器媒体版付费帐户。

### <a name="new-editorial-shot-type-insights-available"></a>新的编辑镜头类型见解可用

视频镜头中添加的新标记可提供编辑“镜头类型”，以使用内容创建工作流中的常用编辑短语对其进行标识，例如：极致特写、特写、广角镜头、中景镜头、双人镜头、室外、室内、左脸和右脸（在 JSON 中可用）。

### <a name="new-people-and-locations-entities-extraction-available"></a>新人员和位置实体提取可用

视频分析器媒体版通过视频的 OCR 和听录借助自然语言处理 (NLP) 来识别指定的位置和人员。 视频分析器媒体版使用机器学习算法在视频中念出特定位置（例如，艾菲尔铁塔）或人员（例如，John Doe）时进行识别。

### <a name="keyframes-extraction-in-native-resolution"></a>以本机分辨率提取关键帧

视频分析器媒体版提取的关键帧以视频的原始分辨率提供。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>用于训练图像中的自定义人脸模型的正式发布版

图像人脸训练从预览模式变为正式发布版（通过 API 和门户提供）。

> [!NOTE]
> 没有与“预览版到正式发布版”过渡相关的价格影响。

### <a name="hide-gallery-toggle-option"></a>隐藏库切换选项

用户可以选择在门户中隐藏库选项卡（类似于隐藏示例选项卡）。
 
### <a name="maximum-url-size-increased"></a>最大 URL 大小已增加

编制视频索引时支持 4096 个字符（而不是 2048 个）的 URL 查询字符串。
 
### <a name="support-for-multi-lingual-projects"></a>多语言项目支持

现在可以根据以不同语言编制索引的视频创建项目（仅限 API）。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>编辑器作为小组件

视频分析器媒体版 AI 编辑器现在作为嵌入到客户应用程序中的小组件提供。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>通过门户更新隐藏式字幕文件的自定义语言模型

客户可以在门户的自定义页中提供 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-analyzer-for-media-deployed-to-japan-east"></a>视频分析器媒体版已部署在日本东部

现在，可以在日本东部区域创建视频分析器媒体版付费帐户。

### <a name="create-and-repair-account-api-preview"></a>创建和修复帐户 API（预览版）

添加了新的 API，使你可以[更新 Azure 媒体服务连接终结点或密钥](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)。

### <a name="improve-error-handling-on-upload"></a>改进上传时的错误处理 

如果基础 Azure 媒体服务帐户配置错误，将返回描述性消息。

### <a name="player-timeline-keyframes-preview"></a>播放机时间线关键帧预览 

现在，可以查看播放机时间线上每个时间的图像预览。

### <a name="editor-semi-select"></a>编辑器准选择

现在，可以查看因在编辑器中选择特定见解时间范围而选中的所有见解的预览。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>从隐藏式字幕文件更新自定义语言模型

[创建自定义语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model)和[更新自定义语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API 现在支持使用 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

调用[更新视频脚本 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript) 时，脚本会自动添加。 与视频关联的训练模型也会自动更新。 有关如何自定义和训练语言模型的信息，请参阅[使用视频分析器媒体版自定义语言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下载脚本格式 - TXT 和 CSV

除了已经支持的隐藏式字幕格式（SRT、VTT 和 TTML）之外，视频分析器媒体版现在还支持以 TXT 和 CSV 格式下载脚本。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
