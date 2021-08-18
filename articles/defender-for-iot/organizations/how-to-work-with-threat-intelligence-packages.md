---
title: 更新威胁情报数据
description: 威胁情报数据包随每个新的 Defender for IoT 版本提供（或者如果在版本之间需要的话提供）。
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f5282002f406c62341f5d104c14aa1cbe9ad32dd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015296"
---
# <a name="threat-intelligence-research-and-packages"></a>威胁情报研究和包 #
## <a name="overview"></a>概述 ##

Microsoft 的安全团队执行专有的 ICS 威胁情报和漏洞研究。 这些团队包括 MSTIC（Microsoft 威胁情报中心），DART（Microsoft 检测和响应团队），DCU（反数字犯罪部门），以及 Section 52（IoT/OT/ICS 领域专家，这些专家跟踪特定于 ICS 的零日漏洞、反向工程恶意软件、市场活动和对手）

团队提供对 Microsoft 的以下各项进行安全检测、分析和响应：

- 云基础结构和服务。
- 传统产品和设备。
- 内部公司资源。

安全团队获得以下权益：

- 防范已知和相关的威胁。
- 有助于会审和设置优先级的见解。
- 在威胁受到影响之前，对其完整上下文的了解。
- 更相关、准确且可操作的数据。

此情报提供上下文信息以丰富 Microsoft 平台分析，并支持公司的托管服务以进行事件响应和违规调查。 威胁情报包包含签名（包括恶意软件签名）、CVE 和其他安全内容。

## <a name="when-are-packages-delivered"></a>何时传递包 ##

威胁情报包大约每个月提供一次，如果需要，可以更频繁地提供。 有关新包的公告，请访问： https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT 。

还可在 Defender for IoT 门户上的“更新”页的“威胁情报更新”部分中查看最新提供的包 。  

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>将威胁情报包更新到传感器 ##

有三个选项可用于将威胁情报包更新到传感器：

- Defender for IoT 传递包时会自动将包推送到传感器。
- 根据需要手动将威胁情报包推送到传感器。
- 下载包，然后将其上传到一个或多个传感器。

具有 Defender for IoT 安全读取者权限的用户可以自动和手动将包推送到传感器。

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>自动将威胁情报更新推送到传感器 ###

威胁情报包可自动更新到云连接传感器，因为它们由 Defender for IoT 发布。 确保自动执行包更新，方法是在启用“自动执行威胁情报更新”选项的情况下，载入云连接传感器。 有关详细信息，请参阅[载入传感器](getting-started.md#onboard-a-sensor)。

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>手动将威胁情报更新推送到传感器 ###

云连接传感器可以通过威胁情报包自动进行更新。 但是，如果想要采取更保守的方法，可以仅在需要时才将包从 Azure Defender for IoT 门户推送到传感器。
这样就可以控制何时安装包，而无需下载包并将其上传到传感器。

**手动推送包：**

1. 转到 Azure Defender for IoT 的“站点和传感器”页面。
1. 对于传感器，选择省略号 (…)，然后选择“推送威胁情报更新”。 “威胁情报更新状态”字段显示更新进度。

#### <a name="change-the-threat-intelligence-update-mode"></a>更改威胁情报更新模式 ####

初始载入后，可以更改传感器威胁情报更新模式。

**更改更新模式：**

1. 对于传感器，选择省略号 (…)，然后选择“编辑”。
1. 启用或禁用“自动执行威胁情报更新”切换。

### <a name="download-packages-and-upload-to-sensors"></a>下载包并将其上传到传感器 ###

可以从 Defender for IoT 门户下载包，并手动将其上传到各个传感器。 如果本地管理控制台管理传感器，则可以将威胁情报包下载到管理控制台，并将它们同时推送到多个传感器。

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="通过 Azure Defender for IoT 门户下载更新。":::

此选项可用于云连接传感器和本地托管传感器 。

**上传到单个传感器：**

1. 转到 Azure Defender for IoT“更新”页。

2. 下载并保存“威胁情报”包。

3. 登录传感器控制台。

4. 在侧边菜单中，选择“系统设置”。

5. 选择“威胁情报数据”，然后选择“更新”。

6. 上传新包。

**同时上传到多个传感器：**

1. 转到 Azure Defender for IoT“更新”页。

2. 下载并保存“威胁情报”包。

3. 登录到管理控制台。

4. 在侧边菜单中，选择“系统设置”。

5. 在“传感器引擎配置”部分中，选择应接收更新包的传感器。  

6. 在“选择威胁情报数据”部分中，选择加号 ( **+** )。

7. 上传该包。

## <a name="review-package-update-status-on-the-sensor"></a>查看传感器上的包更新状态 ##

包更新状态和版本信息显示在传感器的“系统设置”>“威胁情报”部分中 。  

## <a name="review-package-information-for-cloud-connected-sensors"></a>查看云连接传感器的包信息 ##

查看以下有关云连接传感器的威胁情报包的信息：

- 安装的包版本
- 威胁智能更新模式
- 威胁智能更新状态

查看威胁情报信息：

1. 转到 Azure Defender for IoT 的“站点和传感器”页面。
1. 查看每个传感器上安装的“威胁情报版本”。 版本命名基于 Defender for IoT 构建包的日期。
1. 查看“威胁情报模式”。 “自动”指示新的可用包将自动安装在传感器上，因为它们由 Defender for IoT 发布。 “手动”指示你可以根据需要将新的可用包直接推送到传感器。
1. 查看“威胁情报更新状态”。 可能会显示以下状态：

- 失败
- 正在进行
- 更新可用
- 正常

如果云连接的威胁情报更新失败，请在“站点和传感器”页的“传感器状态”和“最后连接的 UTC”列中查看连接信息  。 

## <a name="see-also"></a>另请参阅

[加入传感器](getting-started.md#onboard-a-sensor)

[通过管理控制台管理传感器](how-to-manage-sensors-from-the-on-premises-management-console.md)