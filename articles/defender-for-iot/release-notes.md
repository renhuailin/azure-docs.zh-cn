---
title: Azure Defender for IoT 中的新增功能
description: 本文可帮助你了解最新版本的 IoT 中的新增功能。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: shhazam
ms.openlocfilehash: a8f4b96b27eb09443c2644fd63a8783faaa610e4
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809501"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>用于 IoT 的 Azure Defender 有哪些新功能？

本文列出了适用于 IoT 的新功能和功能增强功能。

记录的功能处于预览阶段。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
## <a name="february-2021"></a>2021 年 2 月

### <a name="enhanced-custom-alert-rules"></a>增强的自定义警报规则

你现在可以创建自定义警报规则，该规则基于天，检测到的一组日期和时间段网络活动。  使用日期和时间规则条件非常有用，例如，在警报严重性发生在警报事件发生时的情况下。 例如，创建自定义规则，该规则在周末或晚上检测到网络活动时触发高严重性警报。

此功能在版本10.1 版的传感器上可用。

### <a name="export-alerts-from-on-premises-management-console"></a>从本地管理控制台导出警报

现在可以从本地管理控制台将警报信息导出到 .csv 文件。 您可以导出检测到的所有警报的信息，或基于筛选视图导出信息。

此功能在本地管理控制台中提供，版本为10.1。
### <a name="device-builder---new-micro-agent-public-preview"></a>设备生成器- (公开预览版的新微型代理) 

新的设备生成器模块可用。 模块称为微代理，允许：

- **与 Azure Iot 中心和 Azure defender For iot 的集成** -通过将其与 Azure iot 中心和 azure Defender for iot 提供的监视选项集成，将更强大的终结点安全性直接构建到 iot 设备中。
- **灵活的部署选项支持标准 iot 操作系统** -可将其部署为二进制包或可修改的源代码，并支持标准 iot 操作系统，如 Linux 和 Azure rto。
- **无操作系统内核依赖项的最小资源需求** ：小的占用量、低 CPU 消耗和无操作系统内核依赖关系。
- **安全状态管理** -主动监视 IoT 设备的安全状况。
- **连续实时 IoT/OT 威胁检测** -检测僵尸网络、暴力强行尝试、加密挖掘者采用和可疑网络活动等威胁

不推荐使用的安全模块文档将移到经典文件夹中。

此功能集适用于当前的公共预览版云版本。

## <a name="january-2021"></a>2021 年 1 月

- [安全性](#security)
- [载入](#onboarding)
- [可用性](#usability)
- [其他更新](#other-updates)
### <a name="security"></a>安全

此版本的证书和密码恢复增强功能。

#### <a name="certificates"></a>证书
  
此版本允许你：

- 将 SSL 证书直接上传到传感器和本地管理控制台。
- 在本地管理控制台和连接的传感器之间以及管理控制台和高可用性管理控制台之间执行验证。 验证基于过期日期、根 CA 真实性和证书吊销列表。  如果验证失败，则会话将不会继续。

对于升级：

- 升级期间，SSL 证书或验证功能不会有任何变化。
- 升级后，传感器和本地管理控制台管理用户可以替换 SSL 证书，或从 "系统设置，SSL 证书" 窗口激活 SSL 证书验证。  

对于全新安装：

- 在首次登录期间，用户需要使用 SSL 证书 (建议) 或本地生成的自签名证书 (不推荐使用) 
- 默认情况下，对于全新安装，证书验证处于启用状态。

#### <a name="password-recovery"></a>密码恢复
  
传感器和本地管理控制台管理用户现在可以从 Azure Defender for IoT 门户恢复密码。 以前的密码恢复要求支持团队介入。

### <a name="onboarding"></a>登记

#### <a name="on-premises-management-console---committed-devices"></a>本地管理控制台提交的设备

初次登录本地管理控制台后，用户现在需要上传激活文件。 文件包含要在组织网络上监视的设备的聚合数量。 此数量称为已提交设备的数目。
提交的设备是在 Azure Defender for IoT 门户上的载入过程中定义的，其中生成激活文件。
第一次用户和用户升级需要上传激活文件。
初始激活之后，在网络上检测到的设备数量可能会超出提交的设备的数量。 例如，如果将更多的传感器连接到管理控制台，则可能发生此事件。 如果检测到的设备数和已提交设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，则应该上载新的激活文件。

#### <a name="pricing-page-options"></a>定价页选项

定价页可让你将新订阅载入 Azure Defender for IoT，并定义网络中已提交的设备。  
此外，定价页现在使你能够管理与传感器关联的现有订阅并更新设备承诺。

#### <a name="view-and-manage-onboarded-sensors"></a>查看和管理已加入的传感器

使用新的站点和传感器门户页面可以：

- 添加有关传感器的描述性信息。 例如，与传感器关联的区域或任意文本标记。
- 查看和筛选传感器信息。 例如，查看有关已连接到云或本地管理的传感器的详细信息，或查看有关特定区域中的传感器的信息。  

### <a name="usability"></a>可用性

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel 新连接器页

Azure Sentinel 中的 Azure Defender for IoT 数据连接器页已重新设计。 数据连接器现在基于订阅而不是 IoT 中心。允许客户更好地管理与 Azure Sentinel 的配置连接。

#### <a name="azure-portal-permission-updates"></a>Azure 门户权限更新  

添加了安全读取器和安全管理员支持。

### <a name="other-updates"></a>其他更新

#### <a name="access-group---zone-permissions"></a>访问组-区域权限
  
本地管理控制台访问组规则将不包含用于授予对特定区域的访问权限的选项。 定义使用站点、区域和业务单元的规则不会有任何变化。   升级后，将修改包含允许访问特定区域的规则的访问组，以允许访问其父站点，包括其所有区域。

#### <a name="terminology-changes"></a>术语更改

术语 "资产" 已在传感器和本地管理控制台、报告和其他解决方案接口中重命名为 "设备"。
在传感器和本地管理控制台警报中，术语 "管理此事件" 已命名为 "修正步骤"。

## <a name="next-steps"></a>后续步骤

[用于 IoT 的 Defender 入门](getting-started.md)
