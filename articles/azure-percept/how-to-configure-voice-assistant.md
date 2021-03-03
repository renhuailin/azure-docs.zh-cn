---
title: 使用 Azure IoT 中心配置语音助手应用程序
description: 使用 Azure IoT 中心配置语音助手应用程序
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662024"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>使用 Azure IoT 中心配置语音助手应用程序

本文介绍如何使用 IoT 中心配置语音助手应用程序。 有关使用演示模板创建语音助手过程的分步教程，请参阅 [使用 Azure Percept Studio 和 Azure Percept 音频生成无代码语音助手](./tutorial-no-code-speech.md)。

## <a name="update-your-voice-assistant-configuration"></a>更新语音助手配置

1. 打开 " [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) "，并在搜索栏中键入 " **IoT 中心** "。 单击该图标以打开 "IoT 中心" 页。

1. 在 "IoT 中心" 页上，选择你的设备已预配到的 IoT 中心。

1. 选择左侧导航菜单中的 "**自动设备管理**" 下的 **IoT Edge** ，查看连接到 IoT 中心的所有设备。

1. 选择您的语音助手应用程序部署到的设备。

1. 单击 " **设置模块**"。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="影像.":::

1. 验证 " **容器注册表凭据** " 部分中是否存在以下条目。 如果需要，请添加凭据。

    |名称|地址|用户名|密码|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. 在 **IoT Edge 模块** "部分中，选择" **azureearspeechclientmodule**"。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="影像.":::

1. 单击 " **模块设置** " 选项卡。验证以下配置：

    |映像 URI|重新启动策略|Desired Status|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |通用|“正在运行”|

    如果您的设置不匹配，请对其进行编辑，然后单击 " **更新**"。

1. 单击 " **环境变量** " 选项卡。验证未定义环境变量。

1. 单击 " **容器创建选项** " 选项卡。验证 **HostConfig** 设置是否与下面显示的设置相匹配。 如果不匹配，则更新设置。

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. 单击 "模块" " **设置** " 选项卡。按如下所示更新 **speechConfigs** 部分：

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > 上面使用的关键字是默认的公开可用关键字。 如果要使用自己的，可以通过将创建的表文件上传到 blob 存储来添加自己的自定义关键字。 需要使用匿名容器访问或匿名 blob 访问来配置 Blob 存储。

## <a name="how-to-find-out-appid-key-and-region"></a>如何查找 appId、key 和 region

若要查找 **appID**、 **key** 和 **Region**，请转到 [Speech Studio](https://speech.microsoft.com/)：

1. 登录并选择相应的语音资源。
1. 在 **Speech Studio** 主页上，单击 "**语音助手**" 下的 "**自定义命令**"。
1. 选择目标项目。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="影像.":::

1. 单击左侧菜单面板上的 " **设置** "。
1. **AppID** 和 **key** 将位于 "**常规** 设置" 选项卡下。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="影像.":::

1. 若要查找你 **所在的区域**，请在 "设置" 中打开 " **LUIS 资源** " 选项卡。 **创作资源** 选择将包含区域信息。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="影像.":::

1. 输入 **speechConfigs** 信息后，单击 " **更新**"。

1. 单击 "**设置模块**" 页顶部的 "**路由**" 选项卡。 确保具有以下值的路由：

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    如果该路由不存在，请添加它。

1. 单击“查看 + 创建”。

1. 单击“创建”。

## <a name="next-steps"></a>后续步骤

更新语音助手配置后，返回到 Azure Percept Studio 中的演示与应用程序进行交互。