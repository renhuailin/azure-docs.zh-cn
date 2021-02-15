---
title: 创建风险评估报告
description: 深入了解单个传感器检测到的网络风险或所有传感器检测到的风险的聚合视图。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d13520a6b2f5e473942d7d0221a4e6f0708b6411
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522251"
---
# <a name="risk-assessment-reporting"></a>风险评估报告

## <a name="about-risk-assessment-reports"></a>关于风险评估报表

风险评估报表提供：

- 组织传感器检测到的设备的总体安全分数。

- 单个传感器检测到的每个网络设备的安全分数。

- 需要改善和安全设备的受攻击设备数、设备数的细分。

-  深入了解安全和操作问题：

    - 配置问题

    - 按安全级别划分的设备漏洞

    - 网络安全问题

    - 网络操作问题

    - 与 ICS 网络的连接

    - Internet 连接

    - 工业恶意软件指示器

    - 协议问题

    - 攻击途径

### <a name="risk-mitigation"></a>风险缓解

报表提供了一些建议，以帮助您提高安全分数。 例如，安装最新的安全更新，将固件升级到最新版本，或跟进警报。

## <a name="about-security-scores"></a>关于安全分数

每个报表中都会生成总体网络安全分数。 分数表示100% 安全的百分比。 例如，如果分数为30%，则表示您的网络安全为30%。

风险评估分数基于从数据包检查、行为建模引擎和 SCADA 特定的状态机设计了解的信息。

**安全设备** 是安全分数高于90% 的设备。

**需要改进的设备**：安全分数介于70% 和89% 之间的设备。

**易受攻击的设备** 是安全性评分低于70% 的设备。

## <a name="create-risk-assessment-reports"></a>创建风险评估报告

创建 PDF 风险评估报告。 报表名称将自动生成为 risk-assessment-report-1.pdf。 将为您创建的每个新报表更新该编号。  此时将显示创建时间和日期。

### <a name="create-a-sensor-risk-assessment-report"></a>创建传感器风险评估报表

基于你登录的传感器所进行的检测创建风险评估报告。

创建报表：

1. 登录到传感器控制台。
1. 选择侧栏菜单上的 " **风险评估** "。
1. 选择 " **生成报告**"。 报表将显示在 "存档的报表" 部分中。
1. 从 "存档的报表" 部分中选择要下载的报表。

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="风险评估的视图。":::

导入公司徽标：

- 选择 " **导入徽标**"。

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>创建本地管理控制台风险评估报表

基于你的本地管理控制台所管理的任何传感器所进行的检测创建风险评估报表。 

创建报表：

1. 选择侧栏菜单上的 " **风险评估** "。

2. 从 " **选择传感器** " 下拉列表中选择一个传感器。

3. 选择 " **生成报告**"。

4. 从 "存档的 **报表**" 部分中选择 "**下载**"。

导入公司徽标：

- 选择 " **导入徽标**"。

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="通过风险评估视图导入徽标。":::

## <a name="next-steps"></a>后续步骤

[攻击向量报告](how-to-create-attack-vector-reports.md)

