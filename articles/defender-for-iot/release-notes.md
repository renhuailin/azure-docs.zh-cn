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
ms.date: 01/06/2021
ms.author: shhazam
ms.openlocfilehash: 72a6e50134647194679055a886b50b01f42e212d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629973"
---
# <a name="whats-new"></a>新增功能

用于 IoT 10.0 的 Defender 提供增强安全性、管理和可用性的功能增强。

## <a name="security"></a>安全性

此版本的证书和密码恢复增强功能。

### <a name="certificates"></a>证书
  
此版本允许你：

- 将 SSL 证书直接上传到传感器和本地管理控制台。
- 在本地管理控制台和连接的传感器之间以及管理控制台和高可用性管理控制台之间执行验证。 验证基于过期日期、根 CA 真实性和证书吊销列表。  如果验证失败，则会话将不会继续。

对于升级：

- 升级期间，SSL 证书或验证功能不会有任何变化。
- 升级后，传感器和本地管理控制台管理用户可以替换 SSL 证书，或从 "系统设置，SSL 证书" 窗口激活 SSL 证书验证。  

对于全新安装：

- 在首次登录期间，用户需要使用 SSL 证书 (建议) 或本地生成的自签名证书 (不推荐使用) 
- 默认情况下，对于全新安装，证书验证处于启用状态。

### <a name="password-recovery"></a>密码恢复
  
传感器和本地管理控制台管理用户现在可以从 Azure Defender for IoT 门户恢复密码。 以前的密码恢复要求支持团队介入。

## <a name="onboarding"></a>登记

### <a name="on-premises-management-console---committed-devices"></a>本地管理控制台提交的设备

初次登录本地管理控制台后，用户现在需要上传激活文件。 文件包含要在组织网络上监视的设备的聚合数量。 此数量称为已提交设备的数目。
提交的设备是在 Azure Defender for IoT 门户上的载入过程中定义的，其中生成激活文件。
第一次用户和用户升级需要上传激活文件。
初始激活之后，在网络上检测到的设备数量可能会超出提交的设备的数量。 例如，如果将更多的传感器连接到管理控制台，则可能发生此事件。 如果检测到的设备数和已提交设备数之间存在差异，则管理控制台中会出现警告。 如果发生此事件，则应该上载新的激活文件。

### <a name="pricing-page-options"></a>定价页选项

定价页可让你将新订阅载入 Azure Defender for IoT，并定义网络中已提交的设备。  
此外，定价页现在使你能够管理与传感器关联的现有订阅并更新设备承诺。

### <a name="view-and-manage-onboarded-sensors"></a>查看和管理已加入的传感器

使用新的站点和传感器门户页面可以：

- 添加有关传感器的描述性信息。 例如，与传感器关联的区域或任意文本标记。
- 查看和筛选传感器信息。 例如，查看有关已连接到云或本地管理的传感器的详细信息，或查看有关特定区域中的传感器的信息。  

## <a name="usability"></a>可用性

### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel 新连接器页

Azure Sentinel 中的 Azure Defender for IoT 数据连接器页已重新设计。 数据连接器现在基于订阅而不是 IoT 中心。允许客户更好地管理与 Azure Sentinel 的配置连接。

### <a name="azure-portal-permission-updates"></a>Azure 门户权限更新  

添加了安全读取器和安全管理员支持。

## <a name="other-updates"></a>其他更新

### <a name="access-group---zone-permissions"></a>访问组-区域权限
  
本地管理控制台访问组规则将不包含用于授予对特定区域的访问权限的选项。 定义使用站点、区域和业务单元的规则不会有任何变化。   升级后，将修改包含允许访问特定区域的规则的访问组，以允许访问其父站点，包括其所有区域。

### <a name="terminology-changes"></a>术语更改

在传感器和本地管理控制台、报告和其他解决方案接口中，术语 "资产已重命名"。
在传感器和本地管理控制台警报中，术语 "管理此事件" 已命名为 "修正步骤"。

## <a name="next-steps"></a>后续步骤

[用于 IoT 的 Defender 入门](getting-started.md)
