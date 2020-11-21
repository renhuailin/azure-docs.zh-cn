---
title: 音频内容创建 - 语音服务
titleSuffix: Azure Cognitive Services
description: 音频内容创建是一个在线工具，可用于为应用和产品自定义和微调 Microsoft 的文本到语音输出。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024953"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>使用音频内容创建工具改进合成

[音频内容创建](https://aka.ms/audiocontentcreation) 是一种易于使用且功能强大的工具，可用于为各种方案（例如 audiobooks、新闻广播、视频旁白和聊天机器人）生成高度自然的音频内容。 使用音频内容创建，你可以微调文本到语音的声音，并以经济高效的方式设计自定义的音频体验。

此工具基于 [语音合成标记语言 (SSML) ](speech-synthesis-markup.md)。 它允许您实时调整文本到语音输出属性，如语音字符、语音样式、说话速度、发音和诗体论。

您可以轻松地在接近 50 150 个不同语言的多个预先构建的语音上进行访问，包括先进的神经 TTS 声音以及您的自定义语音（如果已生成）。 

请参阅 [视频教程](https://www.youtube.com/watch?v=O1wIJ7mts_w) ，了解如何创建音频内容。

## <a name="how-to-get-started"></a>如何开始？

音频内容创建是一个免费工具，但你需要为你使用的 Azure 语音服务付费。 若要使用该工具，需要使用 Azure 帐户登录并创建语音资源。 对于每个 Azure 帐户，你有每月免费语音配额，其中包含500000个字符用于神经 TTS 语音 (每月) ，5000000个字符用于标准和自定义语音 (每月) ，还 (每月1个自定义语音终结点托管服务) 。 每月分配的金额通常足以满足大约3-5 人的小内容团队的规定。 下面是有关如何创建 Azure 帐户并获取语音资源的步骤。 

### <a name="step-1---create-an-azure-account"></a>步骤 1-创建 Azure 帐户

若要使用音频内容创建，需要具有 [Microsoft 帐户](https://account.microsoft.com/account) 和 [Azure 帐户](https://azure.microsoft.com/free/ai/)。 按照这些说明 [设置帐户](./overview.md#try-the-speech-service-for-free)。 

[Azure 门户](https://portal.azure.com/) 是用于管理 Azure 帐户的集中式位置。 你可以创建语音资源、管理产品访问以及监视从简单 web 应用到复杂云部署的所有内容。 

### <a name="step-2---create-a-speech-resource"></a>步骤 2-创建语音资源

注册 Azure 帐户后，需要在 Azure 帐户下创建语音资源以访问语音服务。 查看有关 [如何创建语音资源](./overview.md#create-the-azure-resource)的说明。 

部署新的语音资源需要花费片刻时间。 部署完成后，可以开始创建音频内容。 

 >[!NOTE]
   > 如果计划使用神经声音，请确保在 [支持神经语音的区域](regions.md#standard-and-neural-voices)中创建资源。
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>步骤 3-通过 Azure 帐户和语音资源登录音频内容创建

1. 获取 Azure 帐户和语音资源后，可以通过单击 "**开始**"，登录到 [音频内容创建](https://aka.ms/audiocontentcreation)。
2. 将显示 " **语音资源** " 页面。 选择要处理的语音资源。 单击 "开始" **以** 开始创建音频。 还可以 **通过单击 "新建"** 来创建新的语音资源。 下次登录到音频内容创建工具时，我们会将你直接链接到当前语音资源下的音频工作文件。 
3. 可以随时使用位于顶部导航栏中的“设置”选项来修改语音资源。

## <a name="how-to-use-the-tool"></a>如何使用此工具？

此图显示了微调文本到语音输出所需的步骤。 使用以下链接详细了解每个步骤。

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="微调文本到语音输出所用步骤的示意图":::


1. 选择要处理的语音资源。
2. 使用纯文本或 SSML 脚本[创建音频优化文件](#create-an-audio-tuning-file)。 将你的内容键入或上传到音频内容创建。
3. 选择脚本内容的语音和语言。 音频内容创建包括所有 [Microsoft 文本到语音转换](language-support.md#text-to-speech)。 可使用标准语音、神经语音或者你自己的自定义语音。
   >[!NOTE]
   > 管控访问可用于自定义神经语音，这允许创建类似于自然语音的高清语音。 有关详细信息，请参阅[管控过程](./text-to-speech.md)。

4. 单击 " **播放** " 图标 (三角形) 预览默认合成输出。 然后通过调整发音、停顿、音调、速率、语调、语音风格等来改进输出。 有关选项的完整列表，请参阅[语音合成标记语言](speech-synthesis-markup.md)。 以下 [视频](https://www.youtube.com/watch?v=O1wIJ7mts_w) 演示如何使用音频内容创建来微调语音输出。 
5. 保存并[导出优化音频](#export-tuned-audio)。 在系统中保存优化音轨后，可继续工作并迭代输出。 如果对输出满意，可使用导出功能创建音频创建任务。 可以查看导出任务的状态，并下载用于应用和产品的输出。

## <a name="create-an-audio-tuning-file"></a>创建音频优化文件

有两种方式可以将内容引入音频内容创建工具。

选项 1：

1. 单击右上角的 " **新建文件** " 图标，以创建新的音频优化文件。
2. 在编辑窗口键入或粘贴内容。 每个文件的字符数最多为 20,000 个。 如果脚本的长度超过 20,000 个字符，则可以使用选项 2 将内容自动拆分为多个文件。 
3. 切勿忘记保存。

选项 2：

1. 单击“上传”导入一个或多个文本文件。 支持纯文本和 SSML。 如果脚本文件超过 20,000 个字符，请按段落、字符或正则表达式拆分文件。 
3. 上传文本文件时，请确保该文件满足这些要求。

   | 属性 | 值/注释 |
   |----------|---------------|
   | 文件格式 | 纯文本 (.txt)<br/> SSML 文本 (.txt)<br/> 不支持 Zip 文件 |
   | 编码格式 | UTF-8 |
   | 文件名 | 每个文件必须拥有唯一的名称。 不支持重复项。 |
   | 文本长度 | 文本文件的字符限制为20000。 如果文件超出限制，请按照工具中的说明拆分文件。 |
   | SSML 限制 | 每个 SSML 文件只能包含一条 SSML。 |

**纯文本示例**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML 文本示例**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>导出优化音频

查看音频输出并且对调整和优化都满意后，就可以导出音频。

1. 单击“导出”以创建音频创建任务。 建议选择“导出到音频库”，因为它支持长音频输出和完整的音频输出体验。 还可直接将音频下载到本地磁盘，但只有前 10 分钟可用。
2. 选择优化音频的输出格式。 下面提供了支持格式和采样率的列表。
3. 可在“导出任务”选项卡上查看任务的状态。如果任务失败，请参阅详细信息页获取完整的报表。
4. 完成该任务后，可以在“音频库”选项卡上下载音频。
5. 单击“下载”。 现在，你可以在你的应用或产品中使用自定义的优化音频。

**支持的音频格式**

| 格式 | 16 kHz 采样率 | 24 kHz 采样率 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>如何添加/删除音频内容创建用户？

如果有多个用户要使用音频内容创建，则可以与用户共享你的 Azure 帐户和密码，或向用户授予对 Azure 订阅和语音资源的访问权限。 如果将用户添加到 Azure 订阅，则该用户可以访问 Azure 订阅下的所有资源。 但如果仅将用户添加到语音资源，则该用户将只能访问语音资源，并且不能访问此 Azure 订阅下的其他资源。 有权访问语音资源的用户可以使用音频内容创建。

### <a name="add-users-to-a-speech-resource"></a>将用户添加到语音资源

按照以下步骤将用户添加到语音资源，以便他们可以使用音频内容创建。

1. 在 [Azure 门户](https://portal.azure.com/)中搜索 **认知服务**，选择要将用户添加到的语音资源。
2. 单击“访问控制(IAM)”。 单击“角色分配”选项卡以查看此订阅的所有角色分配。
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="角色分配选项卡":::
1. 单击“添加” > “添加角色分配”以打开“添加角色分配”窗格。 在 "角色" 下拉列表中，选择 " **认知服务用户** 角色"。 如果要授予用户此语音资源的所有权，可以选择 " **所有者** " 角色。
1. 从列表中选择一个用户。 如果在列表中看不到用户，可以在 "选择" 框中键入，以便在目录中搜索显示名称和电子邮件地址。 如果用户不在此目录中，则可以输入 Azure active directory) 信任的用户 [Microsoft 帐户](https://account.microsoft.com/account) (。
1. 单击“保存”以分配该角色。 几分钟后，会在语音资源范围向用户分配认知服务用户角色。

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="添加角色对话框":::

1. 你添加的用户将收到邀请电子邮件。 单击 "**接受邀请**  >  **接受" 加入 Azure** 后，他们可以使用 [音频内容创建](https://aka.ms/audiocontentcreation)。

处于相同语音资源的用户将在音频内容创建工作室中看到彼此的工作。 如果希望每个单独用户在音频内容创建中具有唯一的专用工作区，请为每个用户 [创建新的语音资源](#step-2---create-a-speech-resource) ，并为每个用户提供对语音资源的唯一访问权限。 

### <a name="remove-users-from-a-speech-resource"></a>从语音资源中删除用户
1. 在 Azure 门户中搜索 **认知服务** ，选择要从中删除用户的语音资源。
2. 单击“访问控制(IAM)”。 单击 " **角色分配** " 选项卡，查看此语音资源的所有角色分配。
3. 选择要删除的用户，然后单击 "**删除**  >  **"**。
    :::image source="media/audio-content-creation/remove-user.png" alt-text="“删除”按钮":::

### <a name="enable-users-to-grant-access"></a>使用户能够授予访问权限
如果你希望某个用户向其他用户提供访问权限，则需要为用户提供语音资源的所有者角色，并将用户设置为 Azure 目录读者。 
1. 将用户添加为语音资源的所有者。 请参阅 [如何将用户添加到语音资源](#add-users-to-a-speech-resource)。
    :::image source="media/audio-content-creation/add-role.png" alt-text="角色所有者字段":::
1. 选择左上角的折叠菜单。 单击 " **Azure Active Directory**"，然后单击 " **用户**"。
1. 搜索用户的 Microsoft 帐户，然后前往用户的详细信息页。 单击 " **分配的角色**"。
1. 单击 "**添加分配**  ->  **目录读取器**"。

## <a name="see-also"></a>另请参阅

* [长音频 API](./long-audio-api.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
