---
title: 关于 Cisco ISE pxGrid 集成
description: 将 Defender for IoT 功能与 Cisco ISE pxGrid 桥接，为安全团队提供对企业生态系统前所未有的设备可见性。
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015280"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>关于 Cisco ISE pxGrid 集成

Defender for IoT 提供了由蓝队专家生成的唯一 ICS 和 IoT 网络安全平台，此平台在保护关键国家基础结构方面过往表现不俗，也是唯一已获专利的 ICS 感知威胁分析和机器学习的平台。 Defender for IoT 提供：

- 关于 ICS 设备、漏洞和已知零日威胁的即时见解。

- OT 协议、设备、应用程序及其行为的 ICS 感知深度嵌入式知识。

- 自动化 ICS 威胁建模技术，可通过专有分析来预测目标 ICS 攻击的最可能路径。

## <a name="powerful-device-visibility-and-control"></a>功能强大的设备可见性和控制

Defender for IoT 与 Cisco ISE pxGrid 集成为 OT 态势提供了新水平的集中可见性、监视和控制。

这些桥接的平台能够实现自动化设备可见性，并保护以前无法访问的 ICS 和 IIoT 设备。

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>ICS 和 IIoT 设备可见性：全面和深度

已获专利的 Defender for IoT 技术可确保对企业数据进行全面、深度的 ICS 和 IIoT 设备发现与清单管理。

设备类型、制造商、开放端口、序列号、固件修订、IP 地址和 MAC 地址等数据都是实时更新的。 通过与关键企业数据源集成，Defender for IoT 可以从此基线进一步增强可见性、发现和分析。 例如，CMDB、DNS、防火墙和 Web API。

此外，Defender for IoT 平台结合了被动监视和可选的选择性探测技术，为工业和关键基础结构组织提供最准确且详细的设备清单。

### <a name="bridged-capabilities"></a>桥接功能

将这些功能与 Cisco ISE pxGrid 桥接，为安全团队提供对企业生态系统前所未有的设备可见性。

与 Cisco ISE pxGrid 的无缝、可靠集成，可确保发现所有 OT 设备，并且不会遗漏任何设备信息。

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="终结点类别 OUI 示例。":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="系统中的示例终结点":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="系统中的“属性”的屏幕截图。":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>用例覆盖率：基于 Defender for IoT 属性的 ISE 策略

使用功能强大的基于 Defender for IoT 深度学习的 ISE 策略来处理 ICS 和 IoT 用例要求。

使用策略，可以关闭安全周期，并实时地使网络合规。

例如，客户可以使用 Defender for IoT ICS 和 IoT 属性来创建处理以下用例的策略，例如：

- 创建授权策略，以根据允许的属性（例如，用于 Rockwell Automation PLC 的特定固件版本）允许已知的授权设备进入敏感区域。

- 当在非 OT 区域中检测到 ICS 设备时，通知安全团队。

- 修正供应商过时或不合规的虚拟机。

- 根据需要隔离和阻止设备。

- 生成关于运行包含已知漏洞 (CVE) 的固件的 PLC 或 RTU 的报告。

### <a name="about-this-article"></a>关于本文

本文介绍了如何设置 pxGrid 和 Defender for IoT 平台，以确保 Defender for IoT 将 OT 属性注入到 Cisco ISE 中。

### <a name="getting-more-information"></a>获取更多信息

若要详细了解 Cisco ISE pxGrid 集成要求，请参阅 <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>集成系统要求

本文介绍了集成系统要求：

Defender for IoT 要求

- Defender for IoT 版本 2.5

Cisco 要求

- pxGrid 版本 2.0

- Cisco ISE 版本 2.4

网络要求

- 验证 Defender for IoT 设备是否有权访问 Cisco ISE 管理接口。

- 验证你是否对企业中的所有 Defender for IoT 设备都有 CLI 访问权限。

> [!NOTE]
> 只有具有 MAC 地址的设备才会与 Cisco ISE pxGrid 同步。

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid 设置

本文介绍如何：

- 设置与 pxGrid 的通信

- 订阅终结点设备主题

- 生成证书

- 定义 pxGrid 设置

## <a name="set-up-communication-with-pxgrid"></a>设置与 pxGrid 的通信

本文介绍了如何设置与 pxGrid 的通信。

若要设置通信，请执行以下操作：

1. 登录 Cisco ISE。

1. 依次选择“管理”>“系统”和“部署”。

1. 选择所需节点。 在“常规设置”选项卡中，选中“pxGrid”复选框。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="确保选中“pxGrid”复选框。":::

1. 选择“分析配置”选项卡。

1. 选中“pxGrid”复选框。 添加说明。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="用于添加说明的屏幕截图":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>订阅终结点设备主题

验证 ISE pxGrid 节点是否已订阅终结点设备主题。 依次转到“管理”>“pxGrid 服务”>“Web 客户端”。 在其中，可以验证 ISE 是否已订阅终结点设备主题。

## <a name="generate-certificates"></a>生成证书

本文介绍了如何生成证书。

若要生成证书，请执行以下操作：

1. 依次选择“管理” > “pxGrid 服务”，然后选择“证书”。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="选择“证书”选项卡来生成证书。":::

1. 在“我想要”字段中，选择“生成单个证书(不含证书签名请求)”。

1. 填写其余字段，然后选择“创建”。

1. 创建客户端证书密钥和服务器证书，然后将它们转换为 java 密钥存储格式。 你需要将这些证书复制到网络中的每个 Defender for IoT 传感器。

## <a name="define-pxgrid-settings"></a>定义 pxGrid 设置

若要定义设置，请执行以下操作：

1. 依次选择“管理” > “pxGrid 服务”，然后选择“设置”。

1. 选中“自动批准新的基于证书的帐户”和“允许创建基于密码的帐户”。

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="确保两个复选框都被选中。":::

## <a name="set-up-the-defender-for-iot-appliances"></a>设置 Defender for IoT 设备

本文介绍了如何设置 Defender for IoT 设备来与 pxGrid 通信。 应在所有将数据注入到 Cisco ISE 的 Defender for IoT 设备上执行此配置。

若要设置设备，请执行以下操作：

1. 登录传感器 CLI。

1. 复制之前在传感器上创建的 `trust.jks`。 将其复制到 `/var/cyberx/properties/`。

1. 编辑 `/var/cyberx/properties/pxgrid.properties`：

    1. 添加密钥和信任，然后存储文件名和密码。

    2. 添加 pxGrid 实例的主机名。

    3. `Enabled=true`

1. 重启设备。

1. 对企业中将注入数据的每个设备重复执行这些步骤。

## <a name="view-and-manage-detections-in-cisco-ise"></a>在 Cisco ISE 中查看和管理检测

1. 终结点检测显示在 ISE 上下文“可见性” > “终结点”选项卡中。

1. 依次选择“策略” > “分析” > “添加” > “规则” > “+ 条件” > “新建条件”。

1. 选择“属性”，然后使用 IOT 设备字典根据注入的属性生成分析规则。 注入了以下属性。

    - 设备类型

    - 硬件版本

    - IP 地址

    - MAC 地址

    - 名称

    - 产品 ID

    - 协议

    - 序列号

    - 软件版本

    - Vendor

只有具有 MAC 地址的设备才会同步。

## <a name="troubleshooting-and-logs"></a>故障排除和日志

可以在以下位置找到日志：

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>后续步骤

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
