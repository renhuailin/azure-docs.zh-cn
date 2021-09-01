---
title: 激活和设置传感器
description: 本文介绍如何登录和激活传感器控制台。
ms.date: 04/29/2021
ms.topic: how-to
ms.openlocfilehash: 256e2b941d0eb2f24331abd892b798014cf0eb5d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015936"
---
# <a name="activate-and-set-up-your-sensor"></a>激活和设置传感器

本文介绍如何激活传感器并执行初始设置。

当首次登录和需要激活管理时，管理员用户将执行激活。 设置可确保将传感器配置为最佳检测和发出报警。

安全分析师和只读用户无法激活传感器或生成新密码。

## <a name="sign-in-and-activation-for-administrator-users"></a>管理员用户登录和激活

管理员在首次登录时应验证自己是否有权访问在传感器载入过程中下载的激活和密码恢复文件。 如果无权访问，若要在 Azure Defender for IoT 门户上生成这些文件，他们需要具有 Azure 安全管理员、订阅参与者或订阅所有者权限。

### <a name="first-time-sign-in-and-activation-checklist"></a>首次登录和激活清单

登录传感器控制台之前，管理员用户应有权访问：

- 在安装过程中定义的传感器 IP 地址。

- 传感器的用户登录凭据。 如果为传感器下载了 ISO，则使用在安装过程中收到的默认凭据。 建议在激活后创建新的“管理员”用户。

- 初始密码。 如果从 Arrow 购买了预配置的传感器，则在首次登录时需要生成一个密码。

- 与此传感器关联的激活文件。 该文件是在 Defender for IoT 门户上载入传感器时生成和下载的。

- 公司需要的 SSL/TLS CA 签名证书。

### <a name="about-activation-files"></a>关于激活文件

传感器在特定管理模式下载入到 Azure Defender for IoT：

| 模式类型 | 说明 |
|--|--|
| **云连接模式** | 传感器检测到的信息显示在传感器控制台中。 警报信息也通过 IoT 中心传递，并且可以与其他 Azure 服务（例如 Azure Sentinel）共享。 还可以启用自动威胁情报更新。 |
| **本地连接模式** | 传感器检测到的信息显示在传感器控制台中。 如果将传感器连接到本地管理控制台，则还会与控制台共享检测信息。 |

在加入期间，为此传感器生成并下载了本地连接或云连接的激活文件。 激活文件包含有关传感器管理模式的说明。 应将唯一的激活文件上传到部署的每个传感器。  首次登录时，你需要上传该传感器的相关激活文件。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender for IoT 门户，载入传感器。":::

### <a name="about-certificates"></a>关于证书

安装传感器后，将生成本地自签名证书，该证书用于访问传感器控制台。 管理员首次登录控制台后，系统会提示用户载入 SSL/TLS 证书。

提供两个安全性级别：

- 通过上传 CA 签名的证书来满足组织请求的特定证书和加密要求。
- 允许在管理控制台和连接的传感器之间进行验证。 根据证书吊销列表和证书到期日期对验证进行评估。 如果验证失败，则管理控制台和传感器之间的通信将停止，并且控制台中将出现验证错误。 安装后默认启用此选项。  

控制台支持以下证书类型：

- 专用和企业密钥基础结构（专用 PKI）

- 公钥基础结构（公共 PKI）

- 在设备上本地生成（本地自签名） 

  > [!IMPORTANT]
  > 建议不使用默认的自签名证书。 该证书不安全，应仅用于测试环境。 无法验证证书的所有者，也无法维护系统的安全性。 请勿将此选项用于生产网络。

有关使用证书的详细信息，请参阅[管理证书](how-to-manage-individual-sensors.md#manage-certificates)。

### <a name="sign-in-and-activate-the-sensor"></a>登录并激活传感器

**若要登录并激活，请执行以下操作：**

1. 使用安装过程中定义的 IP 从浏览器转到传感器控制台。 此时将打开“登录”对话框。

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender for IoT 传感器。":::

1. 输入在传感器安装过程中定义的凭据，或选择“密码恢复”选项。 如果从 Arrow 购买了预配置的传感器，请先生成一个密码。 有关密码恢复的详细信息，请参阅[调查初始登录时密码失败的问题](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)。

1. 登录后，将打开“激活”对话框。 选择“上传”，并转到在传感器启动过程中下载的激活文件。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="选择“上传”并转到激活文件。":::

1. 如果要在激活之前更改传感器网络配置，请选择“传感器网络配置”链接。 请参阅[在激活之前更新传感器网络配置](#update-sensor-network-configuration-before-activation)。

1. 接受条款和条件。

1. 选择“激活”  。 此时将打开“SSL/TLS 证书”对话框。

1. 定义证书名称。
1. 上传 CRT 和密钥文件。
1. 输入密码并上传 PEM 文件（如果需要）。
1. 选择“下一步”。 此时将打开“验证”屏幕。 默认情况下，将启用管理控制台与已连接的传感器之间的验证。
1. 关闭“启用系统范围的验证”切换以禁用验证。 建议启用验证。
1. 选择“保存”。  

上传 CA 签名的证书后，你可能需要刷新屏幕。

有关上传新证书、受支持的证书参数以及使用 CLI 证书命令的信息，请参阅[管理单个传感器](how-to-manage-individual-sensors.md)。

#### <a name="update-sensor-network-configuration-before-activation"></a>激活之前更新传感器网络配置  

传感器网络配置参数在软件安装过程中或购买预配置的传感器时定义。 已定义以下参数：

- IP 地址
- DNS
- 默认网关
- 子网掩码
- 主机名

建议在激活传感器之前更新此信息。 例如，你可能需要更改由 Arrow 定义的预配置参数。 你还可以在激活传感器之前定义代理设置。

**若要更新传感器网络配置参数，请执行以下操作：**

1. 从“激活”对话框中选择“传感器网络配置”链接 。

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="传感器网络配置。":::

2. 将显示在安装过程中定义的参数。 还提供了用于定义代理的选项。 根据需要更新任何设置，并选择“保存”。

### <a name="activate-an-expired-license-versions-under-100"></a>激活过期的许可证（低于 10.0 的版本）

对于使用低于 10.0 版本的用户，如果其许可证已过期，将显示以下警报。 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="许可证过期时，需要通过激活文件更新许可证。":::

**激活许可证**：

1. 向[支持人员](https://ms.portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support)提出案例。

1. 向支持人员提供激活 ID 号。

1. 支持人员将以一串字母的形式为你提供新的许可证信息。

1. 阅读条款和条件，并勾选同意复选框。

1. 将字符串粘贴到提供的空白位置。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="将字符串粘贴到提供的字段中。":::

1. 选择“激活”  。

### <a name="subsequent-sign-ins"></a>后续登录

首次激活后，无需激活文件，Azure Defender for IoT 传感器控制台会在登录后打开。 只需你的登录凭据。

登录后，将打开 Azure Defender for IoT 控制台。

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender for IoT 控制台。":::

## <a name="initial-setup-and-learning-for-administrators"></a>初始设置和学习（面向管理员）

首次登录后，Azure Defender for IoT 传感器便开始自动监视网络。 网络设备将显示在设备映射和设备清单部分中。 Azure Defender for IoT 将开始检测网络中发生的所有安全和操作事件并发出警报。 然后，你可以根据检测到的信息创建报告和查询。

最初，此活动是在学习模式下进行的，该模式指示传感器了解网络的常规活动。 例如，传感器会了解在网络中发现的设备、网络中检测到的协议以及特定设备之间生的文件传输。 该活动成为网络的基线活动。

### <a name="review-and-update-basic-system-settings"></a>查看和更新​​基本系统设置

查看传感器的系统设置，以确保将传感器配置为最佳的检测和警报效果。

定义传感器的系统设置。 例如：

- 定义 ICS（或 IoT）和隔离的子网。

- 为特定于站点的协议定义端口别名。

- 定义正在使用的 VLAN 和名称。

- 如果正在使用 DHCP，请定义合法的 DHCP 范围。

- 适当定义与 Active Directory 和邮件服务器的集成。

### <a name="disable-learning-mode"></a>禁用学习模式

调整系统设置之后，可以让 Azure Defender for IoT 传感器在学习模式下运行，直到你认为系统检测可以准确反映网络活动为止。

学习模式应运行约 2 到 6 周，具体取决于网络规模和复杂程度。 禁用学习模式后，任何与基线活动不同的活动都将触发警报。

**若要禁用学习模式，请执行以下操作：**

- 选择“系统设置”，然后关闭“学习”选项 。

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>面向安全分析师和只读用户的首次登录

在登录之前，验证你是否具有：

- 传感器 IP 地址。
- 管理员提供的登录凭据。

## <a name="console-tools-overview"></a>控制台工具：概述

可以从侧面菜单访问控制台工具。

**导航** 

| 窗口 | 图标 | 说明 |
| -----------|--|--|
| 仪表板 | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | 查看网络安全状态的直观快照。 |
| 设备映射 | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | 在映射中查看网络设备、设备连接和设备属性。 可以使用各种缩放、突出显示和筛选选项来显示你的网络。 |
| 设备清单 | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | 设备清单显示此传感器检测到的设备属性的列表。 可用选项如下： <br /> - 根据表格字段对信息进行排序或筛选，然后查看显示的筛选信息。 <br /> - 将信息导出到 CSV 文件。 <br /> - 导入 Windows 注册表详细信息。|
| 警报 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | 在发生策略违规、发生偏离基线行为的行为或检测到网络中任何类型的可疑活动时显示警报。 |
| 报表 | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | 查看基于数据挖掘查询的报表。 |

**分析**

| 窗口| 图标 | 说明 |
|---|---|---|
| 事件时间线 | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | 查看时间线，其中包含有关警报、网络事件（信息性）和用户操作（例如用户登录和用户删除）的信息。|

**导航**

| 窗口 | 图标 | 说明 |
|---|---|---|
| 数据挖掘 | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | 在各个层上生成有关网络设备的全面详细信息。 |
| 调查 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | 查看各种小组件中的趋势和统计信息。 |
| 风险评估 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | 显示“漏洞”窗口。 |

**管理员**

| 窗口 | 图标 | 说明 |
|---|---|---|
| 用户 | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | 定义具有不同访问级别的用户和角色。 |
| 转发 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | 将警报信息转发给合作伙伴、与 Defender for IoT 集成的内部源（例如 Azure Sentinel）、电子邮件地址和 Webhook 服务器等。 <br /> 有关详细信息，请参阅[转发警报信息](how-to-forward-alert-information-to-partners.md)。 |
| 系统设置 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | 配置系统设置。 例如，定义 DHCP 设置、提供邮件服务器详细信息或创建端口别名。 |
| 导入设置 | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | 显示“导入设置”窗口。 你可以在设备信息中进行手动更改。<br /> 有关详细信息，请参阅[导入设备信息](how-to-import-device-information.md)。 |

**支持**

| 窗口| 图标 | 说明 |
|----|---|---|
| 支持 | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | 请联系 [Microsoft 支持部门](https://support.microsoft.com/)获取帮助。 |

## <a name="see-also"></a>另请参阅

[威胁情报研究和包 #](how-to-work-with-threat-intelligence-packages.md)

[加入传感器](getting-started.md#onboard-a-sensor)

[管理传感器激活文件](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)
