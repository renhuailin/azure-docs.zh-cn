---
title: 设置 Azure Percept DK
description: 将开发工具包连接到 Azure 并部署你的第一个 AI 模型
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 77ccf09903b6cff83b52d60d43d0c52533c9c1ad
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041682"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>设置 Azure Percept DK 并部署你的第一个 AI 模型

通过 Azure Percept DK 安装体验开始使用 Azure Percept DK 和 Azure Percept Studio，来将设备连接到 Azure 并部署你的第一个 AI 模型。 验证 Azure 帐户是否与 Azure Percept Studio 兼容后，请完成安装体验，确保将 Azure Percept DK 配置为创建 Edge AI 概念证明。

如果在此快速入门期间遇到任何问题，请参阅[疑难解答](./troubleshoot-dev-kit.md)指南，了解可用的解决方案。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK。
- 基于 Windows、Linux 或 OS X 的主机计算机，具有 Wi-Fi 功能和 Web 浏览器。
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure 帐户必须对订阅拥有“所有者”或“参与者”角色。 详细了解 Azure 角色定义

### <a name="prerequisite-check"></a>先决条件检查

若要验证 Azure 帐户是否是订阅的“所有者”或“参与者”，请按照以下步骤操作。

1. 转到 Azure 门户，使用打算用于 Azure Percept Studio 的 Azure 帐户登录。 

    > [!NOTE]
    > 如果有多个 Azure 帐户，浏览器可能会缓存来自其他帐户的凭据。 请查看疑难解答指南，详细了解如何确保用正确的帐户登录。

1. 展开屏幕左上角的主菜单，然后单击“订阅”或从主页上的图标菜单中选择“订阅”。 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. 从列表中选择你的订阅。 如果在列表中看不到你的订阅，请确保用正确的 Azure 帐户登录。 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
若要创建新的订阅，请执行[这些步骤](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)。

1. 从“订阅”菜单中，选择“访问控制(IAM)”
1. 单击“查看我的访问权限”按钮
1. 查看角色
    - 如果显示“读取者”角色，或者你收到消息指出你没有查看角色的权限，那么你需要按照组织中的必要过程操作，提升你的帐户角色。
    - 如果显示的角色是“所有者”或“参与者”，则你的帐户将适用于 Azure Percept Studio。 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>启动 Azure Percept DK 安装体验

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. 将主计算机直接连接到开发工具包的 Wi-Fi 接入点。 就像连接到任何其他Wi-Fi 网络一样，
    - **网络名称**：scz-xxxx（其中，“xxxx”是开发工具包的 MAC 网络地址的最后四位数）
    - **密码**：可在开发工具包随附的“欢迎使用”卡上找到

    > [!WARNING]
    > 连接到 Azure Percept DK Wi-Fi 接入点时，主计算机将暂时断开与 Internet 的连接。 活动视频会议呼叫、Web 流式处理或其他基于网络的体验将中断，直到完成 Azure Percept DK 安装体验的第 3 步。

1. 一旦连接到开发工具包的 Wi-Fi 接入点，主机设备就会在新的浏览器窗口中自动启动 Azure Percept DK 安装体验。 如果未自动打开，可打开浏览器窗口并导航到 [http://10.1.1.1](http://10.1.1.1) 来手动启动它。 

1. 现在你已启动 Azure Percept 安装体验，接下来可继续完成安装过程。 

    > [!NOTE]
    > Azure Percept DK 安装体验 Web 服务将在未使用 30 分钟后关闭，安装过程完成时也会关闭。 发生这种情况时，建议重启开发工具包，避免与开发工具包 Wi-Fi 接入点存在连接问题。

## <a name="complete-the-azure-percept-dk-setup-experience"></a>完成 Azure Percept DK 安装体验

1. 入门 - 在欢迎屏幕上，单击“下一步”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="欢迎页。":::

1. 连接到 Wi-Fi - 在“网络连接”页面上单击“连接到新的 WiFi 网络”，将开发工具包连接到 Wi-Fi 网络。

    如果之前已将此开发工具包连接到 Wi-Fi 网络，或者已通过 Wi-Fi 网络连接到 Azure Percept DK 安装体验，请单击“跳过”链接。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="连接到 Wi-Fi。":::

1. 从可用连接中选择你的 Wi-Fi 网络，然后单击“连接”。 （将需要你的本地 Wi-Fi 密码）

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="选择 Wi-Fi 网络。"::: 

1. 复制 IP 地址 - 开发工具包成功连接到所选网络后，请记下页面上显示的 IPv4 地址。 **你将需要在本快速入门指南的稍后部分使用此 IP 地址**。 

    > [!NOTE]
    > 每台设备启动时，IP 地址都可能会更改。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="复制 IP 地址。":::

1. 查看并接受许可协议 - 通读许可协议，选择“我已阅读并同意许可协议”（必须滚动到协议底部），然后单击“下一步” 。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="接受 EULA。":::

1. 创建 SSH 登录帐户 - 设置 SSH 以远程访问你的开发工具包。 创建 SSH 用户名和密码。 

    > [!NOTE]
    > SSH（安全外壳）是用于在主机设备与开发工具包之间进行安全通信的网络协议。 有关 SSH 的详细信息，请参阅[本文](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="创建 SSH 帐户。"::: 

1. 开始开发工具包连接过程 - 在下一个屏幕上，单击“使用新设备连接”，开始将开发工具包连接到 Azure IoT 中心的过程。 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="连接到 Azure。"::: 

1. 复制设备代码 - 单击“复制”链接以复制设备代码。 然后，单击“登录 Azure”。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="复制设备代码。"::: 

1. 粘贴设备代码 - 这将打开一个新的浏览器标签页，其中有一个窗口要求输入所复制的设备代码。 将代码粘贴到该窗口中，然后单击“下一步”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="输入设备代码。"::: 

1. 登录到 Azure - 下一个窗口要求你使用在本快速入门开头验证的 Azure 帐户登录。 输入这些登录凭据，然后单击“下一步”。 

    > [!NOTE]
    > 你的浏览器可能会自动缓存其他凭据。 仔细检查是否正在用正确的帐户登录。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="登录到 Azure。":::
 
1. 请勿关闭此步骤中的“安装体验”浏览器标签页 - 登录后，将显示一个屏幕确认你已登录。 尽管这表明你可关闭窗口，但建议不要关闭任何窗口。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="成功登录。"::: 

1. 回到托管安装体验的浏览器标签页。
1. 选择你的“IoT 中心”选项
    - 如果你已有 IoT 中心，并且它在本页上列出，可将其选中并跳到步骤 17。
    - 如果你没有 IoT 中心，或者想要新建一个，请转到列表底部，然后单击“创建新的 Azure IoT 中心”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="选择 IoT 中心。"::: 

1. 创建新的 IoT 中心 - 填写本页上的所有字段。 
    - 选择将用于 Azure Percept Studio 的 Azure 订阅
    - 选择现有资源组。 如果不存在，请单击“新建”并按照提示进行操作。 
    - 选择 Azure 区域。 
    - 为新的 IoT 中心命名
    - 选择定价层（它通常与订阅匹配）

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="创建新的 IoT 中心。"::: 

1. 等待 IoT 中心部署完成 - 可能需要几分钟时间

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="部署 IoT 中心。"::: 

1. 注册开发工具包 - 部署完成后，单击“注册”按钮

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT 中心成功部署。"::: 

1. 对开发工具包命名 - 输入开发工具包的设备名称，然后单击“下一步”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="对设备命名。"::: 

1. 等待下载默认 AI 模型 - 这将需要几分钟时间

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="正在完成安装。"::: 

1. 查看视觉 AI 的实际应用 - 你的开发工具包已成功链接到 Azure IoT 中心，并且已获得默认的视觉 AI 对象检测模型。 Azure Percept Vision 相机现可在不连接到云的情况下进行对象检测推理。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="单击“预览视频输出”。"::: 
       
    > [!NOTE]
    > 此时将关闭加入过程，并断开主计算机的设备 Wi-Fi 访问，但开发工具包会继续连接到 Internet。   可重新启动开发工具包来重启加入体验，这样就可回去完成加入过程，并将设备重新连接到与相同或不同的 Azure 订阅关联的不同 IoT 中心。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="安装体验断开连接警告。"::: 

1. 继续转到 Azure 门户 - 回到“安装体验”窗口，然后单击“继续转到 Azure 门户”按钮，开始在 Azure Percept Studio 中创建自定义 AI 模型。

    > [!NOTE]
    > 验证确保你的主计算机已不再连接到 Wi-Fi 设置中的开发工具包接入点，并且现已重新连接到本地 Wi-Fi。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="转到 Azure Percept Studio。"::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>在 Azure Percept Studio 中查看设备，并部署常见的预生成的示例应用

1. 在 Azure Percept 概述页面查看设备列表。 可在 Azure Percept 概述页面开始访问不同的工作流来进行入门级和高级 AI 边缘模型和解决方案开发

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="查看你的设备列表。":::
    
1. 验证刚刚创建的设备是否已连接，然后单击它。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="选择你的设备。":::

1. 查看设备流，了解开发工具包相机看到的内容。 默认对象检测模型开箱既已部署，它将检测到许多通用对象。

    > [!NOTE]
    > 首次在新设备上执行此操作时，会收到一条通知，指出将在右上角部署新的模块。  完成后，你将能够用相机视频流启动窗口。 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="查看视频流。":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="查看对象检测。":::

1. 了解预生成的示例 AI 模型。   Azure Precept Studio 包含若干常见的预生成示例，只需要单击一下即可部署。   选择“部署示例模型”，以查看并部署这些实例。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="了解预生成的模型。":::
    
1. 将新的预生成示例部署到连接的设备。 从库中选择一个示例，然后单击“部署到设备”

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="查看对象检测的实际应用。":::

## <a name="next-steps"></a>后续步骤

可按照类似流程试用[预生成的语音模型](./tutorial-no-code-speech.md)。