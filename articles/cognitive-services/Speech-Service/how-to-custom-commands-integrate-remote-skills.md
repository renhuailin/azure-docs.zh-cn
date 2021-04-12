---
title: 操作指南：作为远程技能导出自定义命令应用程序 - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将学习如何作为技能导出自定义命令应用程序
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025868"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>作为远程技能导出自定义命令应用程序

在本文中，你将学习如何作为远程技能导出自定义命令应用程序。

## <a name="prerequisites"></a>先决条件
> [!div class="checklist"]
> * [了解 Bot Framework 技能](/azure/bot-service/skills-conceptual)
> * [了解技能清单](https://aka.ms/speech/cc-skill-manifest)
> * [如何从 Bot Framework 机器人调用技能](/azure/bot-service/skills-about-skill-consumers)
> * 现有的自定义命令应用程序。 如果没有任何自定义命令应用程序，请按照“[快速入门：使用自定义命令创建语音助手](quickstart-custom-commands-application.md)”进行试用

## <a name="custom-commands-as-remote-skills"></a>自定义命令作为远程技能
* Bot Framework 技能是可重复使用的对话技术构建基块，涵盖对话用例，让你能够在几分钟内向机器人添加丰富的功能。 有关详细信息，请参阅 [Bot Framework 技能](https://microsoft.github.io/botframework-solutions/overview/skills/)。
* 自定义命令应用程序可以导出为一种技能。 然后，可以通过 Bot Framework 机器人基于远程技能协议调用此技能。

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>配置要显示为远程技能的应用程序

### <a name="application-level-settings"></a>应用程序级别设置
1. 在左侧面板中，选择“设置” > “远程技能”。
1. 将“已启用远程技能”切换到打开。

### <a name="authentication-to-skills"></a>通过身份验证登录技能
1. 如果要启用身份验证，请添加要配置的 Bot Framework 机器人的 Microsoft 应用程序 ID，以便调用自定义命令应用程序。
      > [!div class="mx-imgBorder"]
      > ![将 MSA ID 添加到技能](media/custom-commands/skill-add-msa-id.png)

1. 如果列表中至少添加了一个条目，则会在应用程序上启用身份验证，并且只有允许的机器人才能调用该应用程序。
> [!TIP]
>  如果要禁用身份验证，请从允许列表中删除所有 Microsoft 应用程序 ID。 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>启用/禁用要显示为技能的命令

可以选择要基于远程技能导出哪些命令。

1. 要基于技能显示命令，请在“启用技能命令”下选择“启用新命令”。
1. 从下拉列表中，选择要添加的命令。
1. 选择“保存”。

### <a name="configure-triggering-utterances-for-commands"></a>配置命令的触发表达
自定义命令使用为命令配置的示例语句来生成技能触发表达。 这些触发表达将用于生成“技能清单”的“调度程序”部分[](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)。

作为作者，你可能想要控制哪些示例语句可用于为技能生成触发表达。
1. 默认情况下，清单文件中将包括命令中的所有触发示例。
1. 如果要显式消除任何一个示例，请在“已启用技能命令”部分的命令上选择“编辑”图标。
    > [!div class="mx-imgBorder"]
    > ![编辑已启用的技能命令](media/custom-commands/skill-edit-enabled-command.png)

1. 接下来，对于要忽略的示例语句，右键单击 > “禁用示例语句”。
    > [!div class="mx-imgBorder"]
    > ![禁用示例](media/custom-commands/skill-disable-example-sentences.png)

1. 选择“保存”。
1. 你会发现，在此窗口中无法添加新示例。 如果需要添加，请继续退出设置部分，然后从折叠的“命令”列表中选择相关命令。 这时，即可在“示例语句”部分添加新条目。 这种更改会自动反映到该命令的远程技能设置值中。

> [!IMPORTANT]
> 如果现有的示例语句引用了“字符串”>“目录”数据类型，则技能触发表达列表中将自动忽略这些语句。 

## <a name="download-skill-manifest"></a>下载技能清单
1. 在发布应用程序后，可以下载技能清单文件。
1. 使用技能清单配置 Bot Framework 使用者机器人，以调用自定义命令技能。
> [!IMPORTANT]
> 要下载技能清单，必须发布自定义命令应用程序。 </br>
> 此外，如果对应用程序进行了任何更改，则需要再次发布该应用程序，清单文件中才会反映最新更改。

> [!NOTE]
> 如果在发布应用程序时遇到任何问题，并将错误定向到了技能触发表达，请重新检查“已启用技能命令”配置。 每个显示的命令都必须至少具有一个有效的触发表达。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从客户端更新命令](./how-to-custom-commands-update-command-from-client.md)
