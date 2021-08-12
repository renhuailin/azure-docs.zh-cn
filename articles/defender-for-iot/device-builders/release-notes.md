---
title: 适用于设备生成器的 Azure Defender for IoT 的新增功能
description: 了解 Defender for IoT 设备生成器的最新版本和最新功能。
ms.topic: overview
ms.date: 06/06/2021
ms.openlocfilehash: cde850ba6e5dedddea9d74a8232bc20e8a634c59
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113011037"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>适用于设备生成器的 Azure Defender for IoT 有哪些新增功能？  

本文列出了 Defender for IoT 的新功能和功能增强。

标记的功能目前处于预览阶段。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版本、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Azure Defender for IoT 的版本控制和支持 

下面列出了相关支持、Defender for IoT 的中断性变更策略，以及当前可用的 Azure Defender for IoT 版本。 

### <a name="servicing-information-and-timelines"></a>维护信息和日程表 

Microsoft 计划每季度至少发布一次 Azure Defender for IoT 更新。 Azure Defender for IoT 传感器和本地管理控制台的正式发布 (GA) 版本发布后，最多可获取 9 个月的支持。 修补程序和新功能将适用于当前受支持的当前 GA 版本，但不适用于更旧的 GA 版本。

### <a name="versions-and-support-dates"></a>版本和支持日期

| 版本 | 发布日期 | 结束支持日期 |
|--|--|--|
| 10.0 | 2021/01 | 2021/10 |
| 10.3 | 2021/04 | 2022/02 |

## <a name="april-2021"></a>2021 年 4 月

### <a name="work-with-automatic-threat-intelligence-updates-public-preview"></a>使用自动威胁智能更新（公共预览版）

现可将新的威胁智能包自动推送到云连接传感器，因为它们由 Microsoft Defender for IoT 发布。 还可以下载威胁智能包，然后将其上传到传感器。

使用自动更新有助于降低运营工作量和确保更高的安全性。 启用自动更新：启用“自动威胁智能更新”切换开关后，在 Defender for IoT 门户上载入云连接传感器。

如果想要采用更保守的方法更新威胁智能数据，可以仅在需要时才将包从 Azure Defender for IoT 门户手动推送到云连接传感器。
这样就可以控制何时安装包，而无需下载包并将其上传到传感器。 从 Defender for IoT“站点和传感器”页面手动将更新推送到传感器。

还可以查看有关威胁智能包的下列信息：

- 安装的包版本
- 威胁智能更新模式 
- 威胁智能更新状态

### <a name="view-cloud-connected-sensor-information-public-preview"></a>查看云连接传感器信息（公共预览版）

在“站点和传感器”页面上查看有关云连接传感器的重要操作信息。

- 安装的传感器版本
- 传感器的云连接状态。
- 上次检测到传感器连接到云的时间。

### <a name="alert-api-enhancements"></a>警报 API 的增强功能

使用警报 API 的用户可以使用新字段。

**本地管理控制台**

- 源地址和目标地址
- 修正步骤
- 用户定义的传感器的名称
- 与传感器关联的区域的名称 
- 与传感器关联的站点的名称

**传感器**

- 源地址和目标地址
- 修正步骤

使用新字段时，需要 API 版本 2。

### <a name="features-delivered-as-generally-available-ga"></a>以正式发布版 (GA) 提供的功能

以下功能以前提供的是公共预览版，现已提供正式发布版 (GA)：

- 传感器 - 增强的自定义警报规则
- 本地管理控制台 - 导出警报
- 将第二个网络接口添加到本地管理控制台
- 设备生成器 - 新的微代理

## <a name="march-2021"></a>2021 年 3 月

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>传感器 - 增强的自定义警报规则（公共预览版）

现在可以创建自定义警报规则，该规则基于日期和日期的组合，已检测到时段网络活动。  使用日期和时间规则条件非常有用，例如，警报严重性源自警报事件发生的时间的情况。 例如，创建自定义规则，该规则在周末或晚上检测到网络活动时触发高严重性警报。

此功能在版本 10.2 的传感器上提供。

### <a name="on-premises-management-console---export-alerts-public-preview"></a>本地管理控制台 - 导出警报（公共预览版）

现在可以从本地管理控制台将警报信息导出到 .csv 文件。 可以导出检测到的所有警报的信息或导出基于筛选视图的信息。

此功能在 10.2 版本的本地管理控制台上提供。

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>将第二个网络接口添加到本地管理控制台（公共预览版）

现在可以通过将第二个网络接口添加到本地管理控制台来增强部署的安全性。 此功能可让本地管理在一个安全网络上拥有连接的传感器，同时允许用户通过另一个独立的网络接口访问本地管理控制台。

此功能在 10.2 版本的本地管理控制台上提供。

### <a name="device-builder---new-micro-agent-public-preview"></a>设备生成器 - 新的微代理（公共预览版）

新的设备生成器模块可用。 通过此模块（称为微代理），可以：

- **与 Azure IoT 中心和 Azure Defender for IoT 的集成** - 通过将 IoT 设备与 Azure IoT 中心和 Azure Defender for IoT 提供的监视选项集成，将更强大的终结点安全性构建到 IoT 设备。
- **灵活的部署选项，支持标准 IoT 操作系统** - 可部署为二进制文件包或可修改的源代码，并支持标准 IoT 操作系统，如 Linux 和 Azure RTOS。
- **最小资源需求，无操作系统内核依赖项** - 低占用量、低 CPU 消耗且无操作系统内核依赖项。
- **安全状况管理** - 主动监视 IoT 设备的安全状况。
- **连续实时 IoT/OT 威胁检测** - 检测僵尸网络、暴力攻击尝试、加密货币挖矿者和可疑网络活动等威胁

已弃用的 Defender-IoT-micro-agent 文档将移动到 Agent-based solution for device builders>Classic 文件夹。

此功能集适用于当前的公共预览版云版本。

## <a name="january-2021"></a>2021 年 1 月

- [安全性](#security)
- [载入](#onboarding)
- [可用性](#usability)
- [其他更新](#other-updates)
### <a name="security"></a>安全

此版本的证书和密码恢复功能已增强。

#### <a name="certificates"></a>证书
  
此版本支持：

- 将 SSL 证书直接上传到传感器和本地管理控制台。
- 在管理控制台和连接的传感器之间以及在管理控制台和高可用性管理控制台之间执行验证。 验证基于到期日期、根 CA 真实性和证书吊销列表。  如果验证失败，会话将不会继续。

对于升级：

- 升级期间，SSL 证书或验证功能不会有任何变化。
- 升级后，传感器和本地管理控制台管理用户可以替换 SSL 证书，或从“系统设置”，“SSL 证书”窗口激活 SSL 证书验证。  

对于新安装：

- 在首次登录期间，用户需要使用 SSL 证书（建议）或本地生成的自签名证书（不建议）
- 默认情况下，对于新安装，证书验证处于启用状态。

#### <a name="password-recovery"></a>密码恢复
  
传感器和本地管理控制台管理用户现在可以从 Azure Defender for IoT 门户恢复密码。 以前的密码恢复需要支持团队介入。

### <a name="onboarding"></a>登记

#### <a name="on-premises-management-console---committed-devices"></a>本地管理控制台 - 已提交的设备

初次登录本地管理控制台后，用户现在需要上传激活文件。 文件包含要在组织网络上监视的设备的总数。 此数量称为已提交设备的数目。
已提交的设备是在 Azure Defender for IoT 门户上的载入过程中定义的，激活文件在此门户中生成。
第一次用户登录和用户升级需要上传激活文件。
初始激活之后，在网络上检测到的设备数量可能会超出提交的设备的数量。 例如，如果将更多传感器连接到管理控制台，则可能会发生此事件。 如果检测到的设备数与已提交的设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，应上传新的激活文件。

#### <a name="pricing-page-options"></a>定价页选项

通过定价页，可以将新订阅载入 Azure Defender for IoT，并定义网络中已提交的设备。  
此外，通过定价页，现在可以管理与传感器关联的现有订阅并更新设备承诺。

#### <a name="view-and-manage-onboarded-sensors"></a>查看和管理已加入的传感器

使用新的站点和传感器门户页面可以：

- 添加有关传感器的说明性信息。 例如，与传感器关联的区域或自由文本标记。
- 查看和筛选传感器信息。 例如，查看有关已连接到云或本地管理的传感器的详细信息，或查看有关特定区域中的传感器的信息。  

### <a name="usability"></a>可用性

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel 新建连接器页

Azure Sentinel 中的 Azure Defender for IoT 数据连接器页已重新设计。 数据连接器现在基于订阅而不是 IoT 中心，支持客户更好地管理与 Azure Sentinel 的配置连接。

#### <a name="azure-portal-permission-updates"></a>Azure 门户权限更新  

已添加安全读取者和安全管理员支持。

### <a name="other-updates"></a>其他更新

#### <a name="access-group---zone-permissions"></a>访问组 - 区域权限
  
本地管理控制台访问组规则将不包含用于授予对特定区域的访问权限的选项。 定义使用站点、区域和业务单元的规则不会有任何变化。   升级后，将修改包含允许访问特定区域的规则的访问组，以允许访问其父级站点，包括其所有区域。

#### <a name="terminology-changes"></a>术语更改

术语“资产”已在传感器和本地管理控制台、报告和其他解决方案接口中重命名为“设备”。
在传感器和本地管理控制台警报中，术语“管理此事件”已命名为“修正步骤”。

## <a name="next-steps"></a>后续步骤

[什么是基于代理的设备构建者解决方案](architecture-agent-based.md)
