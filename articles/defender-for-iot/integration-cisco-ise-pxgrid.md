---
title: 关于 Cisco ISE pxGrid 集成
titleSuffix: Azure Defender for IoT
description: 通过 Cisco ISE pxGrid 为 IoT 桥接 Defender 功能，使安全团队成为企业生态系统的空前设备可见性。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/28/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3f1fb099aa18ebec5a7e2063740556cf806302e7
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557411"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>关于 Cisco ISE pxGrid 集成

Defender for IoT 提供了由蓝色团队专家构建的唯一 ICS 和 IoT 网络安全平台，其中包含用于保护关键国家基础结构的跟踪记录，以及具有获专利的可通过 ICS 识别的威胁分析和机器学习的唯一平台。 用于 IoT 的 Defender 提供：

- 立即深入了解 ICS 设备、漏洞和已知的和零天的威胁。

- ICS 感知深层嵌入的有关协议、设备、应用程序及其行为的知识。

- 一种自动化 ICS 威胁建模技术，可通过专有分析预测目标 ICS 攻击的最可能路径。

## <a name="powerful-device-visibility-and-control"></a>强大的设备可见性和控制

用于 IoT 与 Cisco ISE 的 pxGrid 集成，为顶级环境提供了一种新的集中可见性、监视和控制。

这些桥接平台为以前无法访问的 ICS 和 IIoT 设备启用了自动的设备可见性和保护。

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>ICS 和 IIoT 设备可见性：综合性和深度

适用于 IoT 技术的获专利的 Defender 可确保企业数据的全面、深度的 ICS 和 IIoT 设备发现与库存管理。

设备类型、制造商、打开的端口、序列号、固件修订、IP 地址和 MAC 地址数据及更多内容会实时更新。 用于 IoT 的 Defender 可以通过与关键企业数据源集成，进一步提高此基线的可见性、发现和分析能力。 例如，CMDBs、DNS、防火墙和 Web Api。

此外，IoT 平台的 Defender 结合了被动监视和可选选择性探测技术，可为工业和关键基础结构组织中的设备提供最准确且更详细的清单。

### <a name="bridged-capabilities"></a>桥接功能

通过 Cisco ISE pxGrid 桥接这些功能，使安全团队成为企业生态系统的空前设备可见性。

与 Cisco ISE pxGrid 的无缝可靠集成可确保未发现任何设备，并且不会丢失任何设备信息。

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="终结点类别 OUI 的示例。":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="系统中的示例终结点":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="位于系统中的属性的屏幕截图。":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>用例覆盖率：基于 Defender for IoT 属性的 ISE 策略

使用基于 Defender 的用于 IoT 深度学习的强大 ISE 策略来处理 ICS 和 IoT 用例要求。

使用策略可以关闭安全周期并使你的网络能够实时符合要求。

例如，客户可以使用用于 IoT ICS 和 IoT 属性的 Defender 来创建处理以下用例的策略，例如：

- 创建授权策略，以根据允许的属性（例如，针对幼圆自动化 Plc 的特定固件版本）允许已知和授权设备进入敏感区域。

- 在非 OT 区域检测到 ICS 设备时通知安全团队。

- 用过时或不符合要求的供应商修正计算机。

- 根据需要隔离和阻止设备。

-  (标识符) ，生成包含已知漏洞的 Plc 或 RTUs 的报告。

### <a name="about-this-article"></a>关于本文

本文介绍如何设置 pxGrid 和用于 IoT 的 Defender 平台，以确保将 IoT 用于 IoT 将属性注入到 Cisco ISE。

### <a name="getting-more-information"></a>获取详细信息

有关 Cisco ISE pxGrid 集成要求的详细信息，请参阅 <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>集成系统要求

本文介绍了集成系统要求：

用于 IoT 的 Defender 要求

- 用于 IoT 版本2.5 的 Defender

Cisco 要求

- pxGrid 版本2。0

- Cisco ISE 版本2。4

网络要求

- 验证 IoT 设备的 Defender 是否有权访问 Cisco ISE 管理界面。

- 验证你的企业中的所有 Defender for IoT 设备是否具有 CLI 访问权限。

> [!NOTE]
> 只有具有 MAC 地址的设备才与 Cisco ISE pxGrid 同步。

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid 安装程序

本文介绍如何：

- 设置与 pxGrid 的通信

- 订阅终结点设备主题

- 生成证书

- 定义 pxGrid 设置

## <a name="set-up-communication-with-pxgrid"></a>设置与 pxGrid 的通信

本文介绍如何设置与 pxGrid 的通信。

若要设置通信：

1. 登录到 Cisco ISE。

1. 选择 "**管理** > **系统** 和 **部署**"。

1. 选择所需的节点。 在 "常规设置" 选项卡中，选择 " **pxGrid" 复选框**。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="确保选中 &quot;pxgrid&quot; 复选框。":::

1. 选择 " **分析" 配置** 选项卡。

1. 选中 " **pxGrid" 复选框**。 添加说明。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="添加说明的屏幕截图":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>订阅终结点设备主题

验证 ISE pxGrid 节点是否已订阅终结点设备主题。 导航到 "**管理**" " > **pxGrid Services** > **Web 客户端**"。 可以在其中验证 ISE 是否已订阅终结点设备主题。

## <a name="generate-certificates"></a>生成证书

本文介绍如何生成证书。

若要生成：

1. 选择 "**管理**  >  **pxGrid 服务**"，然后选择 "**证书**"。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="选择 &quot;证书&quot; 选项卡以生成证书。":::

1. 在 " **我想** 使用" 字段中，选择 " **生成单个证书 (没有证书签名请求)**。

1. 填写其余字段，然后选择 " **创建**"。

1. 创建客户端证书密钥和服务器证书，然后将其转换为 java 密钥存储格式。 需要将它们复制到网络中的每个 "IoT" 传感器。

## <a name="define-pxgrid-settings"></a>定义 pxGrid 设置

定义设置：

1. 选择 "**管理**  >  **pxGrid 服务**"，然后选择 "**设置**"。

1. 启用 " **自动批准新的基于证书的帐户** "，并 **允许创建基于密码的帐户。**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="确保选中两个复选框。":::

## <a name="set-up-the-defender-for-iot-appliances"></a>设置用于 IoT 设备的 Defender

本文介绍如何设置用于 IoT 设备的 Defender 以与 pxGrid 通信。 应在将数据注入到 Cisco ISE 的 IoT 设备的所有 Defender 上执行此配置。

若要设置设备：

1. 登录传感器 CLI。

1. 复制 `trust.jks` 和，以前在传感器上创建了。 将其复制到： `/var/cyberx/properties/` 。

1. 编辑 `/var/cyberx/properties/pxgrid.properties`：

    1. 添加密钥和信任，并存储文件名和密码。

    2. 添加 pxGrid 实例的主机名。

    3. `Enabled=true`

1. 重新启动设备。

1. 针对企业中将注入数据的每个设备重复这些步骤。

## <a name="view-and-manage-detections-in-cisco-ise"></a>在 Cisco ISE 中查看和管理检测

1. 终结点检测在 ISE 上下文的 "**可见**  >  **终结点**" 选项卡中显示。

1. 选择 "**策略** 分析" "添加规则" 和 "  >    >    >    >  **条件**  >  **创建新条件**"。

1. 选择 " **属性** "，并使用 IOT 设备字典根据插入的属性生成分析规则。 以下属性已注入。

    - 设备类型

    - 硬件修订

    - IP 地址

    - MAC 地址

    - 名称

    - 产品 ID

    - 协议

    - 序列号

    - 软件版本

    - Vendor

仅同步带有 MAC 地址的设备。

## <a name="troubleshooting-and-logs"></a>故障排除和日志

可以在中找到日志：

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
