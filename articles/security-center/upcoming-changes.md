---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6bec9f0a1c22691d818566cec3f59c1ec0f3d3bb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051610"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

- [将发布来自 AWS 的正式版 (GA) 建议](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [两条旧版建议不再将数据直接写入 Azure 活动日志](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [“应用系统更新”安全控制中的两项建议将被弃用](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)
- [弃用 11 种 Azure Defender 警报](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>将发布来自 AWS 的正式版 (GA) 建议

**预计更改日期：** 2021 年 4 月

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

自从引入云连接器后，来自 AWS 安全中心的建议已推出预览版。 标记为“预览”的建议未包含在安全分数的计算中，但仍然应该尽可能地予以修正，以便在预览期结束时，这些建议能够对分数起到作用。

由于此项更改，两组 AWS 建议将过渡到正式版：

- [安全中心的 PCI DSS 控制](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [安全中心的 CIS AWS 基础基准控制](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

当这些功能为正式版并对 AWS 资源运行评估时，结果将影响所有多云资源与混合云资源的合计安全分数。 



### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>两条旧版建议不再将数据直接写入 Azure 活动日志 

**预计更改日期：** 2021 年 3 月

安全中心将几乎所有安全建议的数据传递到 Azure 顾问，后者又将数据写入 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

对于其中两条建议，数据将同时直接写入 Azure 活动日志。 由于此项更改，安全中心会停止将这些旧版安全建议的数据直接写入活动日志， 而我们会将数据导出到 Azure 顾问，如同对所有其他建议所做的那样。 

这两条旧版建议为：
- 应在计算机上解决 Endpoint Protection 运行状况问题
- 应该修复计算机上安全配置中的漏洞

如果你一直在活动日志的“TaskDiscovery 类型的建议”类别中访问这两条建议的信息，现在不再可以这样做。

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>“应用系统更新”安全控制中的两项建议将被弃用 

**预计更改日期：** 2021 年 3 月

以下两项建议计划在 2021 年 2 月弃用：

- **应重启计算机来应用系统更新**。 这可能会对安全功能分数造成轻微影响。
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议“应在计算机上解决 Log Analytics 代理运行状况问题”。 这可能会对安全功能分数造成轻微影响。

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 数据分类建议的增强

**预计更改日期：** 2021 年第 2 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。


### <a name="deprecation-of-11-azure-defender-alerts"></a>弃用 11 种 Azure Defender 警报

**预计更改日期：** 2021 年 3 月

在下个月，我们将弃用下列 11 种 Azure Defender 警报。

- 新警报将取代下面两种警报并扩大覆盖范围：

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 预览版 - 检测到 MicroBurst 工具包“Get-AzureDomainInfo”函数运行 |
    | ARM_MicroBurstRunbook    | 预览版 - 检测到 MicroBurst 工具包“Get-AzurePasswords”函数运行  |
    |                          |                                                                          |

- 下面 9 种警报与已弃用的 Azure Active Directory 标识保护连接器相关：

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 不熟悉的登录属性 |
    | AnonymousLogin      | 匿名 IP 地址          |
    | InfectedDeviceLogin | 受恶意软件感染的 IP 地址     |
    | ImpossibleTravel    | 异常位置登录               |
    | MaliciousIP         | 恶意 IP 地址          |
    | LeakedCredentials   | 凭据泄露            |
    | PasswordSpray       | 密码喷射                |
    | LeakedCredentials   | Azure AD 威胁智能  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。
