---
title: 如何：将自定义命令应用程序导出为远程技能语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将自定义命令应用程序作为技能导出
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025868"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>以远程技能导出自定义命令应用程序

本文介绍如何将自定义命令应用程序作为远程技能导出。

## <a name="prerequisites"></a>先决条件
> [!div class="checklist"]
> * [了解机器人框架技能](/azure/bot-service/skills-conceptual)
> * [了解技能清单](https://aka.ms/speech/cc-skill-manifest)
> * [如何从机器人框架机器人调用技能](/azure/bot-service/skills-about-skill-consumers)
> * 现有的自定义命令应用程序。 如果没有任何自定义命令应用程序，请尝试使用- [快速入门：使用自定义命令创建语音助手](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>自定义命令作为远程技能
* 机器人框架技能是可重复使用的对话技术构建基块，涵盖会话使用案例，使你能够在几分钟内向机器人添加丰富的功能。 若要了解详细信息，请参阅 [Bot Framework 技能](https://microsoft.github.io/botframework-solutions/overview/skills/)。
* 自定义命令应用程序可以作为一种技能导出。 然后，可以通过机器人框架机器人从远程技能协议调用此技能。

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>将应用程序配置为作为远程技能公开

### <a name="application-level-settings"></a>应用程序级别设置
1. 在左侧面板中，选择 "**设置**" "  >  **远程技能**"。
1. **启用 "启用远程技能**" 切换到 "打开"。

### <a name="authentication-to-skills"></a>向技能进行身份验证
1. 若要启用身份验证，请添加要配置的机器人框架 Bot 的 Microsoft 应用程序 Id，以便调用自定义命令应用程序。
      > [!div class="mx-imgBorder"]
      > ![向技能添加 MSA id](media/custom-commands/skill-add-msa-id.png)

1. 如果列表中至少添加了一个条目，则会在应用程序上启用身份验证，并且只有允许的 bot 才能调用该应用程序。
> [!TIP]
>  若要禁用身份验证，请从允许列表中删除所有 Microsoft 应用程序 Id。 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>启用/禁用要作为技能公开的命令

您可以选择要通过远程技能导出哪些命令。

1. 若要通过技巧公开命令，请在 "**启用技能命令**" 下选择 "**启用新命令**"。
1. 从下拉列表中，选择要添加的命令。
1. 选择“保存”。

### <a name="configure-triggering-utterances-for-commands"></a>为命令配置触发最谈话
自定义命令使用为命令配置的示例句子，以便生成最谈话的技能。 这些 **触发最谈话** 将用于生成 **调度** 程序部分的 [**技能清单**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)。

作为作者，你可能想要控制哪些 **示例句子** 用于生成技能的触发最谈话。
1. 默认情况下，命令中的所有 **触发示例** 都将包含在清单文件中。
1. 如果要显式消除任何一个示例，请从 "**技能" 的 "已启用命令**" 部分中选择 "**编辑**" 图标。
    > [!div class="mx-imgBorder"]
    > ![编辑启用了技能的命令](media/custom-commands/skill-edit-enabled-command.png)

1. 接下来，在要省略的示例句子上，**右键单击**"  >  **禁用示例句子**"。
    > [!div class="mx-imgBorder"]
    > ![禁用示例](media/custom-commands/skill-disable-example-sentences.png)

1. 选择“保存”。
1. 你会注意到，你无法在此窗口中添加新的示例。 如果需要执行此操作，请转到 "退出设置" 部分，并从 " **命令** " "可折叠面板" 选择相关命令。 此时，可以在 " **示例句子** " 部分中添加新条目。 此更改将自动反映在该命令的远程技能设置值中。

> [!IMPORTANT]
> 如果您的现有示例句子引用了 **String > Catalog** data-type，则这些句子将自动从触发最谈话列表中忽略。 

## <a name="download-skill-manifest"></a>下载技能清单
1. 之后，你已 **发布** 应用程序，你可以下载技能清单文件。
1. 使用技能清单将机器人框架使用者 bot 配置为调用自定义命令技能。
> [!IMPORTANT]
> 若要下载技能清单，必须 **发布** 自定义命令应用程序。 </br>
> 此外，如果对应用程序进行了 **任何更改** ，则需要再次发布应用程序，以使最新的更改反映在清单文件中。

> [!NOTE]
> 如果在发布应用程序时遇到任何问题，并将错误定向到触发最谈话的技能，请重新检查配置以 **了解技能**。 每个公开的命令都必须至少具有一个有效的触发查询文本。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从客户端更新命令](./how-to-custom-commands-update-command-from-client.md)
