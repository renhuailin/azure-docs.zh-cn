---
title: 设置 Azure Percept DK
description: 将开发工具包连接到 Azure 并部署你的第一个 AI 模型
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3a6a00fd13165deb1a0ec10d3c6d24fed5a5c278
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114709911"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>设置 Azure Percept DK 并部署你的第一个 AI 模型

完成 Azure Percept DK 安装体验，以配置开发工具包并部署第一个 AI 模型。 验证 Azure 帐户是否与 Azure Percept 兼容后，将执行以下操作：

- 将开发工具包连接到 Wi-Fi 网络
- 设置 SSH 登录名以远程访问开发工具包
- 创建新的 IoT 中心，以便与 Azure Percept 一起使用
- 将开发工具包连接到 IoT 中心和 Azure 帐户

如果在此过程中遇到任何问题，请参阅[安装故障排除指南](./how-to-troubleshoot-setup.md)，了解可用的解决方案。

> [!TIP]
> 你可以随时返回到设置体验来重新初始化开发工具包，以便执行连接到新的 Wi-Fi 网络、创建新的 SSH 用户、重新连接到 IoT 中心等操作。

## <a name="prerequisites"></a>必备条件

- Azure Percept DK（开发工具包）。
- 基于 Windows、Linux 或 OS X 的主机计算机，具有 Wi-Fi 功能和 Web 浏览器。
- 具有活动订阅的 Azure 帐户。 [免费创建帐户。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure 帐户必须在订阅中拥有“所有者”或“参与者”角色。 请按照以下步骤检查 Azure 帐户角色。 有关 Azure 角色定义的详细信息，请参阅 [Azure 基于角色的访问控制文档](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。

    > [!CAUTION]
    > 如果有多个 Azure 帐户，浏览器可能会缓存来自其他帐户的凭据。 为避免混淆，建议关闭所有未使用的浏览器窗口，并登录到 [Azure 门户](https://portal.azure.com/)，然后再开始安装过程。 请查看[安装故障排除指南](./how-to-troubleshoot-setup.md)，详细了解如何确保用正确的帐户登录。

### <a name="check-your-azure-account-role"></a>检查 Azure 帐户角色

要验证 Azure 帐户在订阅中是“所有者”还是“参与者”，请按照以下步骤操作：

1. 转到 [Azure 门户](https://portal.azure.com/)，使用打算用于 Azure Percept 的 Azure 帐户登录。

1. 单击“订阅”图标（图标看起来像一把黄色的钥匙）。

1. 从列表中选择你的订阅。 如果看不到订阅，请确保用正确的 Azure 帐户登录。 若要创建新的订阅，请执行[这些步骤](../cost-management-billing/manage/create-subscription.md)。

1. 从“订阅”菜单中，选择“访问控制 (IAM)”。
1. 单击“查看我的访问权限”。
1. 检查角色：
    - 如果角色列示为“读取者”角色，或者收到消息指出没有查看角色的权限，那么需要按照组织中的必要过程操作，提升帐户角色。
    - 如果角色列出为“所有者”或“参与者”，则帐户将适用于 Azure Percept，可以继续执行安装过程。

## <a name="launch-the-azure-percept-dk-setup-experience"></a>启动 Azure Percept DK 安装体验

1. 将主计算机直接连接到开发工具包的 Wi-Fi 接入点。 就像连接到任何其他 Wi-Fi 网络一样，打开计算机上的“网络和 Internet 设置”，选择以下网络，并在出现提示时输入网络密码：

    - **网络名称**：根据开发工具包的操作系统版本，Wi-Fi 接入点的名称为 scz-xxxx 或 apd-xxxx（其中“xxxx”是开发工具包的 MAC 地址的最后四位数） 
    - **密码**：可在开发工具包随附的欢迎卡上找到

    > [!WARNING]
    > 连接到 Azure Percept DK Wi-Fi 接入点时，主计算机将暂时断开与 Internet 的连接。 正在进行的视频会议通话、Web 流式处理或其他基于网络的体验将中断。

1. 一旦连接到开发工具包的 Wi-Fi 接入点，主计算机就会在新的浏览器窗口中自动启动安装体验，并在地址栏中显示 your.new.device/。 如果标签页未自动打开，请在 Web 浏览器中转到 [http://10.1.1.1](http://10.1.1.1) 来启动设置体验。 请确保登录浏览器使用的 Azure 帐户凭据与打算用于 Azure Percept 的帐户凭据相同。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="欢迎页。":::

    > [!CAUTION]
    > 如果 30 分钟未使用，安装体验 Web 服务将自行关闭。 如果发生这种情况，请重启开发工具包，避免发生与开发工具包 Wi-Fi 接入点的连接问题。

## <a name="complete-the-azure-percept-dk-setup-experience"></a>完成 Azure Percept DK 安装体验

1. 在“欢迎”屏幕上，单击“下一步”。

1. 在“网络连接”页上，单击“连接到新的 WiFi 网络”。

    如果已将开发工具包连接到 Wi-Fi 网络，请单击“跳过”。

1. 从可用网络列表中选择 Wi-Fi 网络，然后单击“连接”。 根据提示输入网络密码。

    > [!NOTE]
    > Mac 用户 - 在 Mac 上完成安装过程时，首先会打开窗口而不是打开 Web 浏览器。 连接从设备接入点切换到 Wi-Fi 后，窗口将关闭。 打开 Web 浏览器并转到 https://10.1.1.1 ，并在此完成安装过程。

1. 开发工具包成功连接到所选网络后，该页将显示分配给开发工具包的 IPv4 地址。 **记下页面上显示的 IPv4 地址。** 通过 SSH 连接到开发工具包进行故障排除和设备更新时需要这个 IP 地址。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="复制 IP 地址。":::

    > [!NOTE]
    > 每台设备启动时，IP 地址都可能会更改。

1. 通读许可协议，选择“我已阅读并同意此许可协议”（必须滚动到协议底部），然后单击“下一步”。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="接受 EULA。":::

1. 输入 SSH 帐户名和密码，并 **记下登录信息供以后使用**。

    > [!NOTE]
    > SSH（安全外壳）是一种网络协议，可用于通过主计算机远程连接到开发工具包。

1. 在下一页上，单击“作为新设备安装”，在 Azure 帐户中创建新设备。

1. 单击“复制”以复制设备代码。 然后，单击“登录 Azure”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="复制设备代码。":::

    > [!NOTE]
    > 如果在尝试接收设备代码时收到以下错误消息：无法获取设备代码。请确保设备已连接到 Internet。 最常见的原因是当前网络有问题。  请尝试将以太网电缆插入开发工具包或连接到其他 Wi-Fi 网络，然后重试。  不太常见的原因可能是主计算机的日期/时间已关闭。 

1. 将打开一个新的浏览器标签页，其中显示“输入代码”窗口。 将代码粘贴到该窗口中，然后单击“下一步”。 在安装体验过程中，请勿关闭“欢迎”标签页。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="输入设备代码。":::

1. 使用将用于开发工具包的 Azure 帐户凭据登录到 Azure Percept。 完成后，让浏览器标签页保持打开状态。

    > [!CAUTION]
    > 你的浏览器可能会自动缓存其他凭据。 仔细检查是否正在用正确的帐户登录。

    在设备上成功登录 Azure Percept 后，回到“欢迎”标签页以继续安装体验。

1. “欢迎”标签页上显示“将设备分配到 Azure IoT 中心”页时，采取以下操作之一：

    - 如果已有要用于 Azure Percept 的 IoT 中心，并且它已列于此页上，请将其选中并跳到步骤 15。
    - 如果没有 IoT 中心，或者想要新建一个，请单击“创建新的 Azure IoT 中心”。

    > [!IMPORTANT]
    > 如果有一个 IoT 中心，但没有出现在列表中，则可能使用了错误的凭据登录 Azure Percept。 请参阅[安装故障排除指南](./how-to-troubleshoot-setup.md)以获取帮助。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="选择 IoT 中心。":::

1. 要创建新的 IoT 中心，请完成以下字段：

    - 选择将用于 Azure Percept 的 Azure 订阅。
    - 选择现有资源组。 如果资源组不存在，请单击“新建”并按照提示进行操作。
    - 选择距离所在物理位置最近的 Azure 区域。
    - 为新的 IoT 中心命名。
    - 选择 S1（标准）定价层。

    > [!NOTE]
    > 如果最终需要更高的[消息吞吐量](../iot-hub/iot-hub-scaling.md#message-throughput)用于边缘 AI 应用程序，随时可以在 Azure 门户中[将 IoT 中心升级到更高的标准层](../iot-hub/iot-hub-upgrade.md)。 B 和 F 层不支持 Azure Percept。

1. IoT 中心部署可能需要几分钟时间。 部署完成后，请单击“注册”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT 中心成功部署。":::

1. 输入开发工具包的设备名称，然后单击“下一步”。

1. 等待下载设备模块 – 这将需要几分钟时间。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="正在完成安装。":::

1. 看到“设备安装完成!” 页时，表示开发工具包已成功链接到 IoT 中心，并下载了必要的软件。 开发工具包会自动断开与 Wi-Fi 访问点的连接，进而产生以下两条通知：

    > [!NOTE]
    > 在此设置过程中配置的 IoT Edge 容器使用的证书会在 90 天后过期。 可以通过重启 IoT Edge 来自动重新生成证书。 有关更多详细信息，请参阅[管理 IoT Edge 设备上的证书](../iot-edge/how-to-manage-device-certificates.md)。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="安装体验断开连接警告。":::

1. 将主计算机连接到开发工具包在步骤 2 中连接到的 Wi-Fi 网络。

1. 单击“继续转到 Azure 门户”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-Azure-portal-continue.png" alt-text="转到 Azure Percept Studio。":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>查看开发工具包视频流并部署示例模型

1. 可在 [Azure Percept Studio 概述页面](https://go.microsoft.com/fwlink/?linkid=2135819)开始访问不同的工作流来进行入门级和高级边缘 AI 解决方案开发。 如要开始查看，请单击左侧菜单中的“设备”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="查看你的设备列表。":::

1. 确认开发工具包列为“已连接”，然后单击此开发工具包以查看设备页。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="选择你的设备。":::

1. 选择“查看设备流”。 如果是第一次查看设备的视频流，会在右上角看到一个通知，指示部署了一个新模型。 这可能需要几分钟的时间。

    :::image type="content" source="./media/quickstart-percept-dk-setup/view-stream.png" alt-text="查看视频流。":::

    部署模型后，将收到另一条包含“查看流”链接的通知。 单击此链接可在新的浏览器窗口中查看来自 Azure Percept Vision 照相机的视频流。 开发工具包会预加载 AI 模型，用于自动执行许多常见对象的对象检测。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="查看对象检测。":::

1. Azure Percept Studio 还具有多个示例 AI 模型。 如要将示例模型部署到开发工具包，请导航返回设备页，然后单击“部署示例模型”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/deploy-sample-model.png" alt-text="了解预生成的模型。":::

1. 从库中选择一个示例模型，然后单击“部署到设备”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="查看对象检测的实际应用。":::

1. 成功部署模型后，会在屏幕右上角看到一条包含“查看流”链接的通知。 如要查看实际作用的模型推理，请单击通知中的链接或返回设备页，然后单击“查看设备流”。 以前在开发工具包上运行的任何模型现在都将替换为新模型。

## <a name="video-walkthrough"></a>视频演练

有关上述步骤的直观演练，请参阅以下视频（安装体验从 0:50 开始）：

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建无代码视觉解决方案](./tutorial-nocode-vision.md)