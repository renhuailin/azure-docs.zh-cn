---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/27/2021
ms.author: memildin
ms.openlocfilehash: b8b8b15083711c868add7ac041514bcf1facc30d
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108076880"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改                                                                                                                                                        | 预计更改日期 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [“应用系统更新”安全控制中的两项建议将被弃用](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | 2021 年 4 月                |
| [正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021 年 6 月                 |
| [将发布来自 AWS 的正式版 (GA) 建议](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | 2021 年 8 月           |
| [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 2 季度                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>“应用系统更新”安全控制中的两项建议将被弃用

**预计更改日期：** 2021 年 4 月

以下两项建议将被弃用：

- **应为你的云服务角色更新 OS 版本** - 默认情况下，Azure 会定期将来宾 OS 更新为 OS 系列（在服务配置 (.cscfg) 中指定）中支持的最新映像，例如 Windows Server 2016。
- **Kubernetes Services 应升级到不容易受到攻击的 Kubernetes 版本** - 这项建议的评估覆盖面并不像我们所希望的那样广泛。 这项建议的当前版本最终将替换为增强版本，以更好地与客户的安全需求相匹配。


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013

将从安全中心的监管合规性仪表板中删除 ISO 27001 的旧版实现。 如果要跟踪 ISO 27001 与安全中心的合规性，请针对所有相关管理组或订阅加入新的 ISO 27001:2013 标准，很快将会从仪表板中删除当前的旧版 ISO 27001。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="安全中心的监管合规性仪表板，其中显示了有关删除 ISO 27001 的旧版实现的消息。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>将发布来自 AWS 的正式版 (GA) 建议

**预计更改日期：** 2021 年 8 月

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