---
title: 配置 Azure Percept 语音助理应用程序
description: 使用 Azure IoT 中心配置语音助理应用程序
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ed9a6bcc77e94b569e10a439b044ce78a90faff2
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226818"
---
# <a name="configure-your-azure-percept-voice-assistant-application"></a>配置 Azure Percept 语音助理应用程序

本文介绍如何使用 IoT 中心配置语音助理应用程序。 有关创建语音助理过程的分步教程，请参阅[使用 Azure Percept Studio 和 Azure Percept Audio 生成无代码语音助理](./tutorial-no-code-speech.md)。

## <a name="update-your-voice-assistant-configuration"></a>更新语音助理配置

1. 打开 [Azure 门户](https://portal.azure.com)并在搜索栏中键入“IoT 中心”。 选择用于打开 IoT 中心页的图标。

1. 在 IoT 中心页上，选择设备预配到的 IoT 中心。

1. 在左侧导航菜单的“自动设备管理”下选择“IoT Edge”以查看连接到 IoT 中心的所有设备 。

1. 选择语音助理应用程序部署到的设备。

1. 选择“设置模块”  。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="设备页的屏幕截图，其中突出显示了“设置模块”。":::

1. 验证“容器注册表凭据”部分下是否存在以下条目。 根据需要添加凭据。

    |名称|地址|用户名|密码|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. 在“IoT Edge 模块”部分中，选择“azureearspeechclientmodule” 。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="显示设备上所有 IoT Edge 模块列表的屏幕截图。":::

1. 选择“模块设置”选项卡。验证以下配置：

    映像 URI|重启策略|所需状态
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule: preload-devkit|通用|“正在运行”

    如果设置不匹配，请对其进行编辑并选择“更新”。

1. 选择“环境变量”选项卡。确认没有定义环境变量。

1. 选择“模块孪生设置”选项卡。更新“speechConfigs”部分，如下所示：

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > 上面使用的关键字是默认的公开可用关键字。 如果希望使用自己的关键字，可以通过将创建的表文件上传到 Blob 存储来添加自己的自定义关键字。 需要使用匿名容器访问或匿名 Blob 访问来配置 Blob 存储。

## <a name="how-to-find-out-appid-key-and-region"></a>如何查找 appId、密钥和区域

若要查找 appID、密钥和区域，请转到 [Speech Studio](https://speech.microsoft.com/)  ：

1. 登录并选择适当的语音资源。
1. 在“Speech Studio”主页上，选择“语音助理”下的“自定义命令”  。
1. 选择目标项目。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Speech Studio 中的项目页的屏幕截图。":::

1. 选择左侧菜单面板上的“设置”。
1. appID 和密钥将位于“常规”设置选项卡下  。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="语音项目常规设置的屏幕截图。":::

1. 若要查找你的区域，请在设置中打开“LUIS 资源”选项卡 。 “创作资源”选择将包含区域信息。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="语音项目 LUIS 资源的屏幕截图。":::

1. 输入 speechConfigs 信息后，选择“更新” 。

1. 选择“设置模块”页顶部的“路由”选项卡 。 确保你有一个具有以下值的路由：

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    如果该路由不存在，请添加。

1. 选择“查看 + 创建”  。

1. 选择“创建”。


## <a name="next-steps"></a>后续步骤

更新语音助理配置后，返回到 Azure Percept Studio 中的演示以与应用程序交互。

