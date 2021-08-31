---
title: 删除 Azure Percept Audio 语音助理应用程序
description: 本文介绍如何删除之前创建的语音助理应用程序。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b62ba69695606e04e7044109818c18d30ca21c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778378"
---
# <a name="delete-your-voice-assistant-application"></a>删除语音助理应用程序

这些说明将演示如何从 Azure Percept Audio 设备中删除语音助理应用程序。

## <a name="prerequisites"></a>先决条件

- [先前创建的语音助理应用程序](./tutorial-no-code-speech.md)
- Azure Percept DK 已接通电源，Azure Percept Audio 附件已通过 USB 电缆连接。

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>从 Azure 门户中删除所有语音助理资源

使用完语音助理应用程序后，请按照以下步骤清理在创建应用程序时部署的语音资源。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单面板中选择“资源组”，或在搜索栏中键入“资源组”。

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="显示左侧菜单面板和资源组的 Azure 门户主页的屏幕截图。":::

1. 选择你的资源组。

1. 选择包含应用程序前缀的所有六项资源，然后选择顶部菜单面板上的“删除”图标。

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="已选中待删除的语音资源的屏幕截图。":::

1. 若要确认删除，请在确认框中键入“yes”，验证你选择了正确的资源，然后选择“删除”。

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="删除确认窗口的屏幕截图。":::

> [!WARNING]
> 这会删除用你要删除的语音资源创建的任何自定义关键字，并且语音助理演示将会失效。


## <a name="next-steps"></a>后续步骤
删除语音助理应用程序后，请按照以下教程尝试在 Azure Percept DK 上创建其他应用程序。
- [创建无代码视觉解决方案](./tutorial-nocode-vision.md)
- [创建无代码语音助理应用程序](./tutorial-no-code-speech.md)


