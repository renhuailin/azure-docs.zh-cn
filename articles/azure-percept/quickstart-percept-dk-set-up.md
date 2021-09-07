---
title: 设置 Azure Percept DK 设备
description: 设置 Azure Percept DK 并将其连接到 Azure IoT 中心
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 601970978dc69aea2a6f0947a26cc0bebc1d999d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223028"
---
# <a name="set-up-the-azure-percept-dk-device"></a>设置 Azure Percept DK 设备

完成 Azure Percept DK 设置体验，以配置开发工具包。 验证 Azure 帐户是否与 Azure Percept 兼容后，将执行以下操作：

- 启动 Azure Percept DK 设置体验
- 将开发工具包连接到 Wi-Fi 网络
- 设置 SSH 登录名以远程访问开发工具包
- 在 Azure IoT 中心创建新设备

如果在此过程中遇到任何问题，请参阅[安装故障排除指南](./how-to-troubleshoot-setup.md)，了解可用的解决方案。

> [!NOTE]
> 如果 30 分钟未使用，设置体验 Web 服务将自动关闭。 如果无法连接到开发工具包或看不到其 Wi-Fi 接入点，请重启设备。

## <a name="prerequisites"></a>必备条件

- Azure Percept DK（开发工具包）。
- 基于 Windows、Linux 或 OS X 的主机计算机，具有 Wi-Fi 功能和 Web 浏览器。
- 一个有效的 Azure 订阅。 [免费创建帐户。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 用户必须在订阅中拥有“所有者”或“参与者”角色。 请按照以下步骤检查 Azure 帐户角色。 有关 Azure 角色定义的详细信息，请参阅 [Azure 基于角色的访问控制文档](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。

    > [!CAUTION]
    > 在开始设置体验之前，请关闭所有浏览器窗口，并通过 [Azure 门户](https://portal.azure.com/)登录订阅。 请查看[安装故障排除指南](./how-to-troubleshoot-setup.md)，详细了解如何确保用正确的帐户登录。

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

1. 将主计算机连接到开发工具包的 Wi-Fi 接入点。 选择以下网络，并在提示时输入 Wi-Fi 密码：

    - **网络名称**：scz-xxxx 或 apd-xxxx（其中，xxxx 是开发工具包的 MAC 地址的最后四位数）  
    - **密码**：可在开发工具包随附的欢迎卡上找到

    > [!WARNING]
    > 连接到 Azure Percept DK 的 Wi-Fi 接入点时，主计算机将暂时断开与 Internet 的连接。 正在进行的视频会议通话、Web 流式处理或其他基于网络的体验将中断。

1. 一旦连接到开发工具包的 Wi-Fi 接入点，主计算机就会在新的浏览器窗口中自动启动安装体验，并在地址栏中显示 your.new.device/。 如果标签页未自动打开，请在 Web 浏览器中转到 [http://10.1.1.1](http://10.1.1.1) 来启动设置体验。 请确保登录浏览器使用的 Azure 帐户凭据与打算用于 Azure Percept 的帐户凭据相同。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="欢迎页。":::

    > [!NOTE]
    > **Mac 用户** - 在 Mac 上进行设置体验时，最初会打开一个无法完成设置体验的强制网络门户窗口。 关闭此窗口并打开 Web 浏览器访问 https://10.1.1.1 ，即可完成设置体验。

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>将开发工具包连接到 Wi-Fi 网络

1. 在“欢迎”屏幕上，单击“下一步”。

1. 在“网络连接”页上，单击“连接到新的 WiFi 网络”。

    如果已将开发工具包连接到 Wi-Fi 网络，请单击“跳过”。

1. 从可用网络列表中选择 Wi-Fi 网络，然后单击“连接”。 根据提示输入网络密码。

    > [!NOTE]
    > 建议将此网络设置为“首选网络”(Mac) 或选中“自动连接”框 (Windows)。  如果在此过程中连接中断，此设置将允许主计算机重新连接到开发工具包的 Wi-Fi 接入点。

1. 成功连接开发工具包后，该页将显示分配给开发工具包的 IPv4 地址。 **记下页面上显示的 IPv4 地址。** 通过 SSH 连接到开发工具包进行故障排除和设备更新时需要这个 IP 地址。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="复制 IP 地址。":::

    > [!NOTE]
    > 每台设备启动时，IP 地址都可能会更改。

1. 通读许可协议（必须滚动到协议底部），选择“我已阅读并同意此许可协议”，然后选择“下一步” 。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="接受 EULA。":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>设置 SSH 登录名以远程访问开发工具包

1. 创建 SSH 帐户名称和公钥/密码，然后选择“下一步”。

    如果已经创建 SSH 帐户，则可跳过此步骤。

    记下登录信息以备后用。

    > [!NOTE]
    > SSH（安全外壳）是一种网络协议，可用于通过主计算机远程连接到开发工具包。

##  <a name="create-a-new-device-in-azure-iot-hub"></a>在 Azure IoT 中心创建新设备

1. 选择“设置为新设备”，以在 Azure 帐户中创建新设备。

    现在将从 Azure 获取设备代码。

1. 选择“复制”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="复制设备代码。":::

    > [!NOTE]
    > 如果在后续步骤中使用设备代码时收到错误消息，或者设备代码无法显示，请参阅我们的[故障排除步骤](./how-to-troubleshoot-setup.md)了解详细信息。 

1. 选择“登录到 Azure”。

1. 将打开一个新的浏览器标签页，其中显示“输入代码”窗口。 将代码粘贴到该窗口中，然后单击“下一步”。 在安装体验过程中，请勿关闭“欢迎”标签页。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="输入设备代码。":::

1. 使用将用于开发工具包的 Azure 帐户凭据登录到 Azure Percept。 完成后，让浏览器标签页保持打开状态。

    > [!CAUTION]
    > 你的浏览器可能会自动缓存其他凭据。 仔细检查是否正在用正确的帐户登录。

    在设备上成功登录 Azure Percept 后，选择“允许”。 
    
    返回“欢迎”标签页以继续完成设置体验。

1. “欢迎”标签页上显示“将设备分配到 Azure IoT 中心”页时，采取以下操作之一：

    - 如果此页上列出了 IoT 中心，请跳转到“选择你的 Azure IoT 中心”。
    - 如果没有 IoT 中心，或者想要新建一个，请单击“创建新的 Azure IoT 中心”。

    > [!IMPORTANT]
    > 如果有一个 IoT 中心，但没有出现在列表中，则可能使用了错误的凭据登录 Azure Percept。 请参阅[安装故障排除指南](./how-to-troubleshoot-setup.md)以获取帮助。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="选择 IoT 中心。":::

1. 若要创建新的 IoT 中心，请执行以下操作：

    - 选择将用于 Azure Percept 的 Azure 订阅。
    - 选择现有资源组。 如果资源组不存在，请单击“新建”并按照提示进行操作。
    - 选择距离所在物理位置最近的 Azure 区域。
    - 为新的 IoT 中心命名。
    - 选择 S1（标准）定价层。

    > [!NOTE]
    > IoT 中心部署可能需要几分钟才能完成。 如果边缘 AI 应用程序需要更高的[消息吞吐量](../iot-hub/iot-hub-scaling.md#message-throughput)，随时可以在 Azure 门户中[将 IoT 中心升级到更高的标准层](../iot-hub/iot-hub-upgrade.md)。 B 和 F 层不支持 Azure Percept。

1. 部署完成后，请单击“注册”。

1. 选择你的 Azure IoT 中心

1. 输入开发工具包的设备名称，然后单击“下一步”。

1. 设备模块现在将部署到设备上。 这可能需要几分钟的时间。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="正在完成安装。":::

1. 设备设置完成！ 开发工具包已成功链接到 IoT 中心并部署了所有模块。

    > [!NOTE]
    > 完成后，开发工具包的 Wi-Fi 接入点将自动断开连接，设置体验 Web 服务将终止，并因此生成两个通知。

    > [!NOTE]
    > 在此设置过程中配置的 IoT Edge 容器使用的证书会在 90 天后过期。 可以通过重启 IoT Edge 来自动重新生成证书。 有关更多详细信息，请参阅[管理 IoT Edge 设备上的证书](../iot-edge/how-to-manage-device-certificates.md)。

1. 将主计算机连接到开发工具包连接的 Wi-Fi 网络。

1. 单击“继续转到 Azure 门户”。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="转到 Azure Percept Studio。":::

### <a name="video-walk-through"></a>视频演练 
有关上述步骤的直观演练，请观看以下视频。
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>后续步骤

开发工具包已设置完毕，接下来了解视觉 AI 的实际应用。
- [查看开发工具包视频流](./how-to-view-video-stream.md)
- [将视觉 AI 模型部署到开发工具包](./how-to-deploy-model.md)