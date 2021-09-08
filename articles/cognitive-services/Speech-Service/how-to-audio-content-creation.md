---
title: 音频内容创建 - 语音服务
titleSuffix: Azure Cognitive Services
description: 音频内容创建是一个在线工具，可用于为应用和产品自定义和微调 Microsoft 的文本到语音输出。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: pafarley
ms.openlocfilehash: 2e5942ccbb9e7acf090e5b538dfbb70b1c84762f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538626"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>使用音频内容创建工具改进合成

[音频内容创建](https://aka.ms/audiocontentcreation)是一款易于使用且功能强大的工具，可用于为各种方案（例如有声读物、新闻广播、视频旁白和聊天机器人）生成高度自然的音频内容。 使用音频内容创建，你可以微调文本转语音的声音，并以经济高效的方式设计自定义音频体验。

该工具基于[语音合成标记语言 (SSML)](speech-synthesis-markup.md)。 它允许你实时或以批量合成的方式调整文本转语音的输出属性，如语音字符、语音样式、说话速度、发音和韵律。

可以使用 60 多种不同的语言轻松访问 150 多种预生成语音，包括先进的神经 TTS 语音以及自定义语音（如果已生成）。

有关音频内容创建，请参阅[视频教程](https://youtu.be/ygApYuOOG6w)。

## <a name="how-to-get-started"></a>如何入门？

音频内容创建是一款免费工具，但你需要为你使用的 Azure 语音服务付费。 若要使用该工具，需要使用 Azure 帐户登录并创建语音资源。 对于每个 Azure 帐户，每月都有免费的语音配额，其中包括神经 TTS 语音的 500,000 个字符（每月），标准和自定义语音的 500 万个字符（每月），以及 1 项自定义语音终结点托管服务（每月）。 每月分配的量通常足以满足大约 3-5 人的小型团队。 下面是有关如何创建 Azure 帐户并获取语音资源的步骤。

### <a name="step-1---create-an-azure-account"></a>步骤 1 - 创建 Azure 帐户

若要使用音频内容创建，需要具有 [Microsoft 帐户](https://account.microsoft.com/account) 和 [Azure 帐户](https://azure.microsoft.com/free/ai/)。 按照这些说明[设置帐户](./overview.md#try-the-speech-service-for-free)。

[Azure 门户](https://portal.azure.com/)是用于管理 Azure 帐户的集中位置。 你可以创建语音资源、管理产品访问以及监视从简单 Web 应用到复杂云部署的所有内容。

### <a name="step-2---create-a-speech-resource"></a>步骤 2 - 创建语音资源

注册 Azure 帐户后，需要在 Azure 帐户下创建语音资源以访问语音服务。 查看有关[如何创建语音资源](./overview.md#create-the-azure-resource)的说明。

部署新的语音资源需要花费片刻时间。 部署完成后，就可以开始音频内容创建之旅。

 > [!NOTE]
   > 如果计划使用神经语音，请确保在[支持神经语音的区域](regions.md#neural-and-standard-voices)中创建资源。

### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>步骤 3 - 通过 Azure 帐户和语音资源登录音频内容创建

1. 获取 Azure 帐户和语音资源后，可以通过单击“开始”，登录到[音频内容创建](https://aka.ms/audiocontentcreation)。
2. 主页列出了 Speech Studio 下的所有产品。 单击“音频内容创建”即可开始。
3. 此时会显示“欢迎使用 Speech Studio”页面，你可以在其中设置语音服务。 选择你要使用的 Azure 订阅和语音资源。 单击“使用资源”以完成设置。 下次登录到音频内容创建工具时，我们会将你直接链接到当前语音资源下的音频工作文件。 可以在 [Azure 门户](https://portal.azure.com/)中检查 Azure 订阅的详细信息和状态。 如果你没有可用的语音资源，但你是 Azure 订阅的所有者或管理员，则还可以通过单击“创建新资源”在 Speech Studio 中创建新的语音资源。 如果你是特定 Azure 订阅的用户角色，则可能无权创建新的语音资源。 请联系管理员来获取语音资源访问权限。 
4. 可以随时使用位于顶部导航栏中的“设置”选项来修改语音资源。
5. 如果要切换目录，请转到“设置”或要操作的配置文件。 

## <a name="how-to-use-the-tool"></a>如何使用工具？

此图显示了微调文本到语音输出所需的步骤。 使用以下链接详细了解每个步骤。

:::image type="content" source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="微调文本转语音输出所用步骤的图表":::

1. 选择需要处理的语音资源。
2. 使用纯文本或 SSML 脚本[创建音频优化文件](#create-an-audio-tuning-file)。 键入或将内容上传到音频内容创建。
3. 选择脚本内容的语音和语言。 音频内容创建包括所有 [Microsoft 文本到语音转换](language-support.md#text-to-speech)。 可使用标准语音、神经语音或者你自己的自定义语音。
   > [!NOTE]
   > 管控访问可用于自定义神经语音，这允许创建类似于自然语音的高清语音。 有关详细信息，请参阅[管控过程](./text-to-speech.md)。

4. 选择要预览的内容，然后单击播放图标（一个三角形）来预览默认的合成输出。 请注意，如果你对文本做出任何更改，则需要单击“停止”图标，然后再次单击播放图标，以使用已更改的脚本重新生成音频。  
5. 通过调整发音、停顿、音调、速率、语调、语音风格等来改进输出。 有关选项的完整列表，请参阅[语音合成标记语言](speech-synthesis-markup.md)。 有关如何使用音频内容创建来微调语音输出，请观看此处的[视频](https://youtu.be/ygApYuOOG6w)。
6. 保存并[导出优化音频](#export-tuned-audio)。 在系统中保存优化音轨后，可继续工作并迭代输出。 如果对输出满意，可使用导出功能创建音频创建任务。 可查看导出任务的状态，并下载用于应用和产品的输出。

## <a name="create-an-audio-tuning-file"></a>创建音频优化文件

有两种方式可以将内容引入音频内容创建工具。

选项 1：

1. 单击“新建” > “文件”以创建新的音频优化文件 。
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
   | 文本长度 | 文本文件的字符限制为 20,000。 如果文件超出限制，请按照工具中的说明拆分文件。 |
   | SSML 限制 | 每个 SSML 文件只能包含一条 SSML。 |

**纯文本示例**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

**SSML 文本示例**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
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

如果有多个用户要使用音频内容创建，则可以授予用户对 Azure 订阅和语音资源的访问权限。 如果将用户添加到 Azure 订阅，则该用户可以访问 Azure 订阅下的所有资源。 但如果仅将用户添加到语音资源，则该用户将只能访问语音资源，并且不能访问此 Azure 订阅下的其他资源。 有权访问语音资源的用户可以使用音频内容创建。

用户需要准备一个 [Microsoft 帐户](https://account.microsoft.com/account)。 如果用户没有 Microsoft 帐户，则请创建一个，只需几分钟。 用户可以使用现有电子邮件并将其作为 Microsoft 帐户进行关联，也可以创建新的 Outlook 电子邮件作为 Microsoft 帐户。


### <a name="add-users-to-a-speech-resource"></a>将用户添加到语音资源

按照以下步骤将用户添加到语音资源，以便他们可以使用音频内容创建。

1. 在 [Azure 门户](https://portal.azure.com/)中搜索“认知服务”，选择需要添加用户的语音资源。
2. 单击“访问控制(IAM)”。 单击“角色分配”选项卡以查看此订阅的所有角色分配。
    :::image type="content" source="media/audio-content-creation/access-control-roles.png" alt-text="角色分配选项卡":::
3. 单击“添加” > “添加角色分配”以打开“添加角色分配”窗格。  在“角色”下拉列表中，选择“认知服务用户”角色。 如果要授予用户此语音资源的所有权，可以选择“所有者”角色。
4. 键入用户的电子邮件地址，然后在目录中选择用户。 电子邮件地址必须是 Azure 活动目录信任的 Microsoft 帐户。 用户可以使用个人电子邮件地址轻松注册 [Microsoft 帐户](https://account.microsoft.com/account)。 
5. 单击“保存”以分配该角色。 
6. 用户会收到电子邮件邀请。 通过单击电子邮件中的“接受邀请” > “接受加入 Azure”来接受邀请。  然后，用户会被重定向到 Azure 门户。 用户无需在 Azure 门户中执行进一步操作。 片刻之后，系统会在语音资源范围内为用户分配角色，使其有权访问此语音资源。 如果用户未收到邀请电子邮件，你可以在“角色分配”下搜索用户帐户，然后进入用户配置文件。 找到“标识”->“已接受邀请”，然后单击“(管理)”以重新发送电子邮件邀请。 还可以将邀请链接复制给用户。 
7. 用户现在访问或刷新[音频内容创建](https://aka.ms/audiocontentcreation)产品页面，并使用用户的 Microsoft 帐户进行登录。 在所有语音产品中选择“音频内容创建”块。 在弹出窗口或页面右上角的设置中选择语音资源。 如果用户找不到可用的语音资源，请检查你是否在正确的目录中。 若要检查正确的目录，请单击右上角的帐户配置文件，然后单击“当前目录”旁边的“切换”。 如果有多个目录可用，则意味着你有权访问多个目录。 切换到不同的目录并转到设置，查看正确的语音资源是否可用。 

    :::image type="content" source="media/audio-content-creation/add-role-first.png" alt-text="添加角色对话框":::


处于相同语音资源的用户可在音频内容创建工作室中看到彼此的工作。 如果希望每个单独用户在音频内容创建中具有唯一的专用工作区，请为每个用户[创建新的语音资源](#step-2---create-a-speech-resource)，并为每个用户提供对语音资源的唯一访问权限。

### <a name="remove-users-from-a-speech-resource"></a>从语音资源中删除用户

1. 在 Azure 门户中搜索“认知服务”，选择要删除用户的语音资源。
2. 单击“访问控制(IAM)”。 单击“角色分配”选项卡以查看此语音资源的所有角色分配。
3. 选择要删除的用户，然后单击“删除” > “确定”。
    :::image type="content" source="media/audio-content-creation/remove-user.png" alt-text="“删除”按钮":::

### <a name="enable-users-to-grant-access"></a>使用户能够授予访问权限

如果你希望某个用户向其他用户提供访问权限，则需要为用户提供语音资源的所有者角色，并将用户设置为 Azure 目录读者。
1. 将用户添加为语音资源的所有者。 请参阅[如何将用户添加到语音资源](#add-users-to-a-speech-resource)。
    :::image type="content" source="media/audio-content-creation/add-role.png" alt-text="角色所有者字段":::
2. 在 [Azure 门户](https://portal.azure.com/)中，选择左上角的已折叠菜单。 单击“Azure Active Directory”，然后单击“用户”。
3. 搜索用户的 Microsoft 帐户，然后转到用户的详细信息页。 单击“分配的角色”。
4. 单击“添加分配” -> “目录读者”。 如果按钮“添加分配”灰显，则意味着你没有访问权限。 只有此目录的全局管理员可以向用户添加分配。

## <a name="see-also"></a>另请参阅

* [长音频 API](./long-audio-api.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
