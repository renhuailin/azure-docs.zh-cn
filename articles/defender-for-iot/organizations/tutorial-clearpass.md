---
title: 将 ClearPass 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Azure Defender for IoT 与 ClearPass 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: 7ff16da99f994ff4b708f0fb6f4a40e72f61df78
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129421193"
---
# <a name="tutorial-integrate-clearpass-with-azure-defender-for-iot"></a>教程：将 ClearPass 与 Azure Defender for IoT 集成

本教程帮助你了解如何将 ClearPass Policy Manager (CPPM) 与 Azure Defender for IoT 集成。

Defender for IoT 平台提供连续的 ICS 威胁监视和设备发现功能，将对工业协议、设备和应用程序的深入理解与特定于 ICS 的行为异常情况检测、威胁情报、风险分析和自动威胁建模相结合。

Defender for IoT 会检测和发现 OT 和 ICS 终结点，对其进行分类，并使用 ClearPass Security Exchange 框架和开放 API 直接与 ClearPass 共享信息。

Defender for IoT 会自动用终结点分类数据和若干自定义安全属性更新 ClearPass Policy Manager 终结点数据库。

利用此集成可完成以下操作：

- 查看 Defender for IoT 安全引擎识别的 ICS 和 SCADA 安全威胁。

- 查看 Defender for IoT 传感器发现的设备清单信息。 利用传感器可集中查看整个 IT 和 OT 基础结构中的所有网络设备及终结点。 从这里，可在 ClearPass 系统中定义和管理集中式终结点和边缘安全策略。

在本教程中，你将了解：

> [!div class="checklist"]
> - 创建 ClearPass API 用户
> - 创建 ClearPass 操作员配置文件
> - 创建 ClearPass OAuth API 客户端
> - 配置 Defender for IoT 来与 ClearPass 进行集成
> - 定义 ClearPass 转发规则
> - 监视 ClearPass 与 Defender for IoT 的通信

## <a name="prerequisites"></a>先决条件

### <a name="aruba-clearpass-requirements"></a>Aruba ClearPass 要求

CPPM 在预装了软件的硬件设备上运行，或在以下虚拟机监控程序下作为虚拟机运行。 不支持在客户端计算机（如 VMware Player）上运行的虚拟机监控程序。

- VMware ESXi 5.5、6.0、6.5、6.6 或更高版本。

- Microsoft Hyper-V Server 2012 R2 或 2016 R2。

- Microsoft Windows Server 2012 R2 或 2016 R2 上的 Hyper-V。

- CentOS 7.5 或更高版本上的 KVM。

### <a name="defender-for-iot-requirements"></a>Defender for IoT 要求

- Defender for IoT 2.5.1 或更高版本。

- 一个 Azure 帐户。 如果还没有 Azure 帐户，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-clearpass-api-user"></a>创建 ClearPass API 用户

作为这两种产品之间信道的一部分，Defender for IoT 使用多种 API（TIPS 和 REST）。 通过用户名和密码组合凭据来验证对 TIPS API 的访问。 此用户 ID 必须具有最低访问级别。 不要使用超级管理员配置文件，而是按如下所示使用 API 管理员。

**若要创建 ClearPass API 用户**：

1. 在左侧窗格中选择“管理” > “用户和特权”，然后选择“添加”  。

1. 在“添加管理员用户”对话框中，设置以下参数：

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="管理员用户对话框视图的屏幕截图。":::

    | 参数 | 说明 |
    |--|--|
    | **UserID** | 输入用户 ID。 |
    | **名称** | 输入用户名。 |
    | **密码** | 输入密码。 |
    | **启用用户** | 验证是否启用了此选项。 |
    | **特权级别** | 选择“API 管理员”。 |

1. 选择“添加”。

## <a name="create-a-clearpass-operator-profile"></a>创建 ClearPass 操作员配置文件

Defender for IoT 使用 REST API 作为集成的一部分。 REST API 在 OAuth 框架下进行身份验证。 若要与 Defender for IoT 同步，需要创建 API 客户端。

为了安全地访问 API 客户端的 REST API，请创建一个具有受限访问权限的操作员配置文件。

**若要创建 ClearPass 操作员配置文件**：

1. 导航到“编辑操作员配置文件”窗口。

1. 将所有选项设置为“无访问权限”，以下选项除外：

| 参数 | 说明 |
|--|--|
| **API 服务** | 设置为“允许访问” |
| **Policy Manager** | 指定以下设置： <br />- “字典 - 属性”设置为“读取、写入、删除”  <br />- “字典 - 指纹”设置为“读取、写入、删除”  <br />- “标识 - 终结点”设置为“读取、写入、删除”   |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="编辑操作员配置文件的屏幕截图。":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="Policy Manager 屏幕中选项的屏幕截图。":::

## <a name="create-a-clearpass-oauth-api-client"></a>创建 ClearPass OAuth API 客户端

1. 在主窗口中，选择“管理员” > “API 服务” > “API 客户端”  。

1. 在“创建 API 客户端”选项卡中，设置以下参数：

    - **操作模式**：此参数用于对 ClearPass 进行 API 调用。 选择“ClearPass REST API - 客户端”。

    - **操作员配置文件**：使用之前创建的配置文件。

    - **授权类型**：设置为“客户端凭据(grant_type = client_credentials)”。

1. 务必记录客户端密码和客户端 ID。 例如，`defender-rest`。

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="“创建 API 客户端”的屏幕截图。":::

1. 在 Policy Manager 中，确保在继续下一步之前收集了以下信息列表。

    - CPPM UserID

    - CPPM UserId 密码

    - CPPM OAuth2 API 客户端 ID

    - CPPM OAuth2 API 客户端密码

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>配置 Defender for IoT 来与 ClearPass 进行集成

若要在 ClearPass 中查看设备清单，需要设置 Defender for IoT-ClearPass 同步。同步配置完成后，Defender for IoT 平台会在发现新的终结点时更新 ClearPass Policy Manager EndpointDb。

**若要在 Defender for IoT 传感器上配置 ClearPass 同步**：

1. 在 Defender for IoT 传感器中，选择左侧面板中的“系统设置”。

1. 在“系统设置”窗格中，选择 :::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="左侧的 ClearPass 图标的屏幕截图":::。

1. 设置以下参数：

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="在“系统设置”窗格中填写必填信息的屏幕截图。":::

    - **启用同步：** 启用 Defender for IoT 与 ClearPass 之间的同步

    - **同步频率：** 定义同步频率（分钟）。 默认值为 60 分钟。 最小值为 5 分钟。

    - **ClearPass IP 地址：** 与 Defender for IoT 同步的 ClearPass 系统的 IP 地址。

    - **客户端 ID：** 在 ClearPass 上创建的客户端 ID，用于将数据与 Defender for IoT 同步。

    - **客户端密码：** 在 ClearPass 上创建的客户端密码，用于将数据与 Defender for IoT 同步。

    - **用户名：** ClearPass 管理员用户。

    - **密码：** ClearPass 管理员密码。

1. 选择“保存”  。

## <a name="define-a-clearpass-forwarding-rule"></a>定义 ClearPass 转发规则

若要在 Aruba 中查看 Defender for IoT 发现的警报，需要设置转发规则。 此规则定义 Defender for IoT 安全引擎识别的要发送到 ClearPass 的 ICS 和 SCADA 安全威胁相关信息。

**若要在 Defender for IoT 传感器上定义 ClearPass 转发规则**：

1. 在 Defender for IoT 传感器中，选择左侧面板中的“转发”。

1. 在“转发”窗格中，选择“创建转发规则” 。

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="“转发”窗格的屏幕截图，其中显示了其所有选项。":::

1. 添加规则的名称和严重性，然后在“操作”下拉列表中选择“发送到” > “ClearPass”  。

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="创建转发规则的屏幕截图。":::

1. 在“操作”窗格中，设置以下参数：

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="在“操作”窗格中选择操作。":::

    | 参数 | 说明 |
    |--|--|
    | **主机** | 键入 ClearPass 服务器 IP 地址。 |
    | **端口** | 键入完成转发的 ClearPass 的端口。 |
    | **配置** | 设置以下选项，允许在 ClearPass 系统中查看 Defender for IoT 警报： <br />- **报告非法函数代码：** 协议冲突 - 违反 ICS 协议规范的非法字段值（潜在攻击）。<br />- **报告未经授权的 PLC 编程和固件更新：** 未经授权的 PLC 更改。<br />- **报告未经授权的 PLC 停止：** PLC 停止（故障时间）。<br />- **报告与恶意软件相关的警报：** 行业恶意软件尝试，例如 TRITON、NotPetya 等。<br />- **报告未经授权的扫描：** 未经授权的扫描（潜在侦查）。 |

1. 选择“提交”。

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>监视 ClearPass 与 Defender for IoT 的通信

同步启动后，终结点数据将直接填充到 Policy Manager EndpointDb 中，你可在集成配置屏幕上查看上次更新时间。

**若要查看上次同步到 ClearPass 的时间**：

1. 登录 Defender for IoT 传感器。

1. 在左侧面板中选择“系统设置”。

1. 选择“ClearPass”。

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="查看上次同步的时间和日期的屏幕截图。":::

如果同步无效或显示错误，则可能是未捕获某些信息。 请重新检查记录的数据，此外还可选择“来宾” > “管理” > “支持” > “应用程序日志”来查看 Defender for IoT 与 ClearPass 之间的 API 调用   。

下面是 Defender for IoT 与 ClearPass 之间的 API 日志示例。

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Defender for IoT 与 ClearPass 之间的 API 日志。":::

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何开始进行 ClearPass 集成。 请继续了解 CyberArk。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](./tutorial-cyberark.md)
