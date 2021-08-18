---
title: 创建风险评估报告
description: 深入了解单个传感器检测到的网络风险或所有传感器检测到的风险的聚合视图。
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 567660d1e1e55ee3dde0cbf5e5c09748cbb2cf0c
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015844"
---
# <a name="risk-assessment-reporting"></a>风险评估报告

## <a name="about-risk-assessment-reports"></a>关于风险评估报告

风险评估报告提供：

- 组织传感器检测到的设备的总体安全分数。

- 单个传感器检测到的每个网络设备的安全分数。

- 易受攻击设备数、需要改进的设备数和安全设备数明细。

-  关于安全和操作问题的见解：

    - 配置问题

    - 按安全级别确定优先级的设备漏洞

    - 网络安全问题

    - 网络操作问题

    - ICS 网络连接

    - Internet 连接

    - 行业恶意软件指示器

    - 协议问题

    - 攻击途径

### <a name="risk-mitigation"></a>风险缓解

报告提供了建议来帮助你提高安全分数。 例如，安装最新的安全更新程序、将固件升级到最新版本或跟进警报。

## <a name="about-security-scores"></a>关于安全分数

每个报告都生成总体网络安全分数。 此分数表示安全百分比（总分为 100%）。 例如，如果分数为 30%，则表示网络安全率为 30%。

风险评估分数是基于从数据包检查、行为建模引擎和特定于 SCADA 的状态机设计中了解到的信息。

安全设备：是安全分数在 90% 以上的设备。

需要改进的设备：是安全分数介于 70% 和 89% 之间的设备。

易受攻击设备：是安全分数低于 70% 的设备。

### <a name="about-backup-and-anti-virus-servers"></a>关于备份和防病毒服务器

如果你没有在传感器中定义备份和防病毒服务器地址，风险评估分数可能会受到负面影响。 添加这些地址可以提高分数。 默认情况下，没有定义这些地址。
风险评估报表封面页将指示备份服务器和防病毒服务器是否未定义。

添加服务器的步骤：

1. 依次选择“系统设置”和“系统属性”。
1. 选择“漏洞评估”，然后向“backup_servers”和“AV_addresses”  字段添加地址。 使用逗号分隔多个地址。  用逗号分隔。  
1. 选择“保存”。
## <a name="create-risk-assessment-reports"></a>创建风险评估报告

创建 PDF 风险评估报告。 报告名称自动生成为 risk-assessment-report-1.pdf。 对于你创建的每个新报告，数字都会更新。  创建日期和时间是显示的。

### <a name="create-a-sensor-risk-assessment-report"></a>创建传感器风险评估报告

根据所登录传感器的检测结果，创建风险评估报告。

创建报表：

1. 登录传感器控制台。
1. 选择侧菜单中的“风险评估”。
1. 选择“生成报告”。 此时，报告显示在“存档的报告”部分中。
1. 在“存档的报告”部分中，选择要下载的报告。

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="风险评估视图。":::

若要导入公司徽标，请执行以下操作：

- 选择“导入徽标”。

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>创建本地管理控制台风险评估报告

根据本地管理控制台托管的任何传感器的检测结果，创建风险评估报告。 

创建报表：

1. 选择侧菜单中的“风险评估”。

2. 在“选择传感器”下拉列表中选择传感器。

3. 选择“生成报告”。

4. 在“存档的报告”部分中，选择“下载”。

若要导入公司徽标，请执行以下操作：

- 选择“导入徽标”。

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="通过风险评估视图导入徽标。":::

## <a name="see-also"></a>另请参阅

[攻击途径报告](how-to-create-attack-vector-reports.md)

