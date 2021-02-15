---
title: 激活和设置传感器
description: 本文介绍如何登录和激活传感器控制台。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 56f6c9d3ff3093c3acd28b670e37f823ad5e9f9a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522506"
---
# <a name="activate-and-set-up-your-sensor"></a>激活和设置传感器

本文介绍如何激活传感器并执行初始设置。

当首次登录时和需要激活管理时，管理员用户将执行激活。 安装程序可确保传感器配置为经过最佳检测和发出警报。

安全分析师和只读用户无法激活传感器或生成新密码。

## <a name="sign-in-and-activation-for-administrator-users"></a>管理员用户的登录和激活

首次登录时，管理员应该验证他们是否有权访问在传感器载入期间下载的激活和密码恢复文件。 如果没有，则需要 Azure 安全管理员、订阅参与者或订阅所有者权限，才能在 Azure Defender for IoT 门户上生成这些文件。

### <a name="first-time-sign-in-and-activation-checklist"></a>首次登录和激活清单

在登录到传感器控制台之前，管理员用户应该有权访问：

- 在安装过程中定义的传感器 IP 地址。

- 传感器的用户登录凭据。 如果下载了该传感器的 ISO，请使用在安装过程中收到的默认凭据。 建议在激活后创建新的 *管理员* 用户。

- 初始密码。 如果从箭头购买预配置的传感器，则首次登录时需要生成密码。

- 与此传感器关联的激活文件。 此文件是在 IoT 门户 Defender 上的传感器载入过程中生成和下载的。

- 公司需要的 SSL/TLS CA 签名证书。

### <a name="about-activation-files"></a>关于激活文件

传感器已在特定管理模式下载入到 Azure Defender 用于 IoT：

| 模式类型 | 说明 |
|--|--|
| **云连接模式** | 传感器检测到的信息显示在传感器控制台中。 警报信息也通过 IoT 中心提供，可与其他 Azure 服务（如 Azure Sentinel）共享。 |
| **本地连接模式** | 传感器检测到的信息显示在传感器控制台中。 如果传感器连接到本地管理控制台，还会与本地管理控制台共享检测信息。 |

在载入期间，为此传感器生成并下载了本地连接的或云连接的激活文件。 激活文件包含传感器的管理模式说明。 *应将唯一的激活文件上传到部署的每个传感器。*  首次登录时，需要上载此传感器的相关激活文件。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender for IoT 门户，板载传感器。":::

### <a name="about-certificates"></a>关于证书

安装传感器后，将生成一个本地自签名证书，并使用该证书来访问传感器控制台。 管理员首次登录到控制台之后，系统会提示该用户加入 SSL/TLS 证书。

提供两个安全级别：

- 通过上传 CA 签名的证书来满足组织要求的特定证书和加密要求。
- 允许在管理控制台和连接的传感器之间进行验证。 对照证书吊销列表和证书到期日期对验证进行评估。 *如果验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误。* 默认情况下，安装后会启用此选项。  

控制台支持以下证书类型：

- 专用 PKI 和企业密钥基础结构 (专用 PKI) 

- 公共密钥基础结构 (公共 PKI) 

- 在设备上本地生成 (本地自签名)  

  > [!IMPORTANT]
  > 建议你不要使用默认的自签名证书。 证书不安全，只应用于测试环境。 无法验证证书的所有者，并且无法维护系统的安全。 请勿将此选项用于生产网络。

### <a name="sign-in-and-activate-the-sensor"></a>登录并激活传感器

登录和激活：

1. 使用在安装过程中定义的 IP，从浏览器中转到传感器控制台。 此时将打开 "登录" 对话框。

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="用于 IoT 传感器的 Azure Defender。":::

1. 输入在安装传感器时定义的凭据，或选择 " **密码恢复** " 选项。 如果从箭头购买预配置的传感器，请先生成密码。 有关密码恢复的详细信息，请参阅 [在首次登录时调查密码失败](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)。

1. 登录后，将打开 " **激活** " 对话框。 选择 " **上传** "，并中转到在传感器载入过程中下载的激活文件。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="选择 &quot;上传&quot; 并前往激活文件。":::

1. 如果要在激活之前更改传感器网络配置，请选择 " **传感器网络配置** " 链接。 请参阅 [更新传感器网络配置，然后再激活](#update-sensor-network-configuration-before-activation)。

1. 接受条款和条件。

1. 选择“激活”  。 "SSL/TLS 证书" 对话框将打开。

1. 定义证书名称。
1. 上传 CRT 和密钥文件。
1. 如果需要，请输入通行短语并上传 PEM 文件。
1. 选择“下一步”。 验证屏幕将打开。 默认情况下，会在管理控制台和连接的传感器之间启用验证。
1. 关闭 " **启用系统范围的验证** 切换" 以禁用验证。 建议启用验证。
1. 选择“保存” 。  

上载 CA 签名的证书后，你可能需要刷新屏幕。

有关上传新证书、支持的证书参数和使用 CLI 证书命令的信息，请参阅 [管理单个传感器](how-to-manage-individual-sensors.md)。

#### <a name="update-sensor-network-configuration-before-activation"></a>激活前更新传感器网络配置  

传感器网络配置参数是在软件安装过程中或在购买预配置的传感器时定义的。 已定义以下参数：

- IP 地址
- DNS
- 默认网关
- 子网掩码
- 主机名

在激活传感器之前，您可能需要更新该信息。 例如，你可能需要更改由箭头定义的预配置参数。 你还可以在激活传感器之前定义代理设置。

更新传感器网络配置参数：

1. 从 "**激活**" 对话框中选择 "**传感器网络配置**" 链接。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="传感器网络配置。":::

2. 将显示在安装过程中定义的参数。 此外还提供了用于定义代理的选项。 根据需要更新任何设置，然后选择 " **保存**"。

### <a name="subsequent-sign-ins"></a>后续登录

第一次激活后，在不需要激活文件的情况下登录后，将打开 Azure Defender 的 IoT 传感器控制台。 只需你的登录凭据。

登录后，将打开 "Azure Defender IoT" 控制台。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="用于 IoT 的 Azure Defender 控制台。":::

## <a name="initial-setup-and-learning-for-administrators"></a>管理员) 的初始安装和学习 (

首次登录后，Azure Defender for IoT 传感器将开始自动监视你的网络。 网络设备将显示在设备映射和设备清单部分中。 Azure Defender for IoT 将开始检测并提醒你网络中发生的所有安全和操作事件。 然后，你可以基于检测到的信息创建报表和查询。

最初，此活动是在学习模式下执行的，它指示传感器了解网络的常见活动。 例如，传感器会了解网络中发现的设备、网络中检测到的协议以及特定设备之间发生的文件传输。 此活动将成为网络的基准活动。

### <a name="review-and-update-basic-system-settings"></a>查看并更新基本系统设置

查看传感器的系统设置，确保传感器配置为经过最佳检测和发出警报。

定义传感器的系统设置。 例如：

- 定义 ICS (或 IoT) 和隔离子网。

- 为特定于站点的协议定义端口别名。

- 定义使用中的 Vlan 和名称。

- 如果正在使用 DHCP，请定义合法的 DHCP 范围。

- 适当地定义与 Active Directory 和邮件服务器的集成。

### <a name="disable-learning-mode"></a>禁用学习模式

调整系统设置后，你可以让 Azure Defender for IoT 传感器在学习模式下运行，直到你认为系统检测能准确反映你的网络活动。

学习模式应运行大约2到6周，具体取决于你的网络大小和复杂性。 禁用学习模式后，任何与基线活动不同的活动都将触发警报。

禁用学习模式：

- 选择 " **系统设置** " 并关闭 " **学习** " 选项。

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>安全分析师和只读用户的首次登录

在登录之前，请验证你是否具有：

- 传感器 IP 地址。
- 管理员提供的登录凭据。

## <a name="console-tools-overview"></a>控制台工具：概述

从侧边菜单访问控制台工具。

**导航** 

| 窗口 | 图标 | 说明 |
| -----------|--|--|
| 仪表板 | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | 查看网络安全状态的直观快照。 |
| 设备映射 | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | 查看地图中的网络设备、设备连接和设备属性。 可以使用各种缩放、突出显示和筛选选项来显示你的网络。 |
| 设备清单 | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | 设备清单显示此传感器检测到的设备属性的列表。 可用选项如下： <br /> -排序或根据表字段筛选信息，并查看显示的筛选信息。 <br /> -将信息导出到 CSV 文件。 <br /> -导入 Windows 注册表详细信息。|
| 警报 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | 在策略冲突发生时显示警报，发生与基准行为的偏差，或检测到网络中任何类型的可疑活动。 |
| 报表 | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | 查看基于数据挖掘查询的报表。 |

**分析**

| 窗口| 图标 | 说明 |
|---|---|---|
| 事件时间线 | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | 查看包含警报信息、网络事件 (信息性) 和用户操作（例如用户登录和用户删除）的时间线。|

**导航**

| 窗口 | 图标 | 说明 |
|---|---|---|
| 数据挖掘 | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | 在不同的层上生成有关网络设备的全面的详细信息。 |
| 调查 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | 查看范围广泛的小组件中的趋势和统计信息。 |
| 风险评估 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | 显示 " **漏洞** " 窗口。 |

**管理员**

| 窗口 | 图标 | 说明 |
|---|---|---|
| 用户 | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | 定义具有不同访问级别的用户和角色。 |
| 转发 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | 将警报信息转发给合作伙伴，并将内部源 (例如，Azure Sentinel) 与 Defender for IoT 集成、电子邮件地址、webhook 服务器等。 <br /> 有关详细信息，请参阅 [转发警报信息](how-to-forward-alert-information-to-partners.md) 。 |
| 系统设置 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | 配置系统设置。 例如，定义 DHCP 设置，提供邮件服务器详细信息，或创建端口别名。 |
| 导入设置 | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | 显示 " **导入设置** " 窗口。 你可以在设备信息中执行手动更改。<br /> 有关详细信息，请参阅 [导入设备信息](how-to-import-device-information.md) 。 |

**支持**

| 窗口| 图标 | 说明 |
|----|---|---|
| 支持 | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | 联系 [Microsoft 支持部门](https://support.microsoft.com/) 获取帮助。 |

## <a name="next-steps"></a>后续步骤

[加入传感器](getting-started.md#4-onboard-a-sensor)

[管理传感器激活文件](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)
