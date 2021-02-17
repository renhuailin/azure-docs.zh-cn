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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555136"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

- [“应用系统更新”安全控制中的两项建议将被弃用](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)
- [弃用 11 种 Azure Defender 警报](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>“应用系统更新”安全控制中的两项建议将被弃用 

**预计更改日期：** 2021 年 2 月

以下两项建议计划在 2021 年 2 月弃用：

- **应重启计算机来应用系统更新**。 这可能会对安全功能分数造成轻微影响。
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议“应在计算机上解决 Log Analytics 代理运行状况问题”。 这可能会对安全功能分数造成轻微影响。

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 数据分类建议的增强

**预计更改日期：** 2021 年第 2 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。


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
