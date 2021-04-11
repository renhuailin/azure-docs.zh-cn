---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 6204be2ff52b8aac89b93ac09337b1560255e11d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491876"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改                                                                                                                                                        | 预计更改日期 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 条建议在不同安全控制之间进行了调动](#21-recommendations-moving-between-security-controls)                                                           | 2021 年 4 月                |
| [“应用系统更新”安全控制中的两项建议将被弃用](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | 2021 年 4 月                |
| [将发布来自 AWS 的正式版 (GA) 建议](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | 2021 年 4 月                |
| [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 2 季度                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 条建议在不同安全控制之间进行了调动 

**预计更改日期：** 2021 年 4 月

以下建议将被移动到其他的安全控制。 安全控制是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 这一调动确保了每个建议都处于最适当的控制之下，以满足其目标。 

请在安全控件及其建议中了解每个安全控件中的建议。

|建议 |更改和影响  |
|---------|---------|
|应对 SQL Server 启用漏洞评估<br>应对 SQL 托管实例启用漏洞评估<br>应立即修正 SQL 数据库的漏洞<br>应修正 VM 中的 SQL 数据库漏洞     |从“修正漏洞”（得 6 分）<br>变为“修正安全配置“（得 4 分）。<br>根据你的环境，这些建议会减少对评分的影响。|
|应该为你的订阅分配了多个所有者<br>自动化帐户变量应进行加密<br> IoT 设备 - 经审核的进程已停止发送事件<br> IoT 设备 - 操作系统基线验证失败<br> IoT 设备 - 需要进行 TLS 加密套件升级<br> IoT 设备 - 打开设备上的端口<br> IoT 设备 - 在其中一个链中找到了宽容防火墙策略<br> IoT 设备 - 在输入链中找到了宽容防火墙规则<br> IoT 设备 - 在输出链中找到了宽容防火墙规则<br>应启用 IoT 中心的诊断日志<br> IoT 设备 - 代理正在发送未充分利用的消息<br>IoT 设备 - 默认 IP 筛选策略应为“拒绝”<br>IoT 设备 - IP 筛选器规则为“大范围 IP”<br>IoT 设备 - 应调整代理消息间隔和大小<br>IoT 设备 - 完全相同的身份验证凭据<br>IoT 设备 - 经审核的进程停止发送事件<br>IoT 设备 - 应修复操作系统 (OS) 基线配置|移到 **实施安全最佳做法**。<br>如果某条建议移到“实施安全最佳做法”控制（不得分），则这条建议不再影响安全分数。|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>“应用系统更新”安全控制中的两项建议将被弃用

**预计更改日期：** 2021 年 4 月

以下两项建议将被弃用：

- **应为你的云服务角色更新 OS 版本** - 默认情况下，Azure 会定期将来宾 OS 更新为 OS 系列（在服务配置 (.cscfg) 中指定）中支持的最新映像，例如 Windows Server 2016。
- **Kubernetes Services 应升级到不容易受到攻击的 Kubernetes 版本** - 这项建议的评估覆盖面并不像我们所希望的那样广泛。 这项建议的当前版本最终将替换为增强版本，以更好地与客户的安全需求相匹配。


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>将发布来自 AWS 的正式版 (GA) 建议

**预计更改日期：** 2021 年 4 月

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

自从引入云连接器后，来自 AWS 安全中心的建议已推出预览版。 标记为“预览”的建议未包含在安全分数的计算中，但仍然应该尽可能地予以修正，以便在预览期结束时，这些建议能够对分数起到作用。

由于此项更改，两组 AWS 建议将过渡到正式版：

- [安全中心的 PCI DSS 控制](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [安全中心的 CIS AWS 基础基准控制](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

当这些功能为正式版并对 AWS 资源运行评估时，结果将影响所有多云资源与混合云资源的合计安全分数。 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 数据分类建议的增强

**预计更改日期：** 2021 年第 2 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。



## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。