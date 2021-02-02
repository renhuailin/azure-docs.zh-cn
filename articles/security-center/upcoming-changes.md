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
ms.openlocfilehash: 99dadea37a519289120fcf30e394df1e0f7af5e7
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757705"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

- [Kubernetes 工作负载保护建议即将发布正式版 (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [“应用系统更新”安全控制中的两项建议将被弃用](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Kubernetes 工作负载保护建议即将发布正式版 (GA)

**预计更改日期：** 2021 年 1 月

[保护 Kubernetes 工作负载](kubernetes-workload-protections.md)中介绍的 Kubernetes 工作负载保护建议当前为预览版。 虽然建议目前为预览版，但它不会导致资源运行不正常，在计算安全功能分数时也不会包含这些建议。

这些建议即将发布正式版 (GA)，因此将会包含在分数计算中。 如果尚未对其进行修正，则可能会对安全功能分数造成轻微影响。

请尽量进行修正（请参阅[修正 Azure 安全中心内的建议](security-center-remediate-recommendations.md)了解具体操作）。

Kubernetes 工作负载保护建议如下：

- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项
- 应强制执行容器 CPU 和内存限制
- 应避免特权容器
- 应强制对容器使用不可变（只读）根文件系统
- 应避免使用特权提升的容器
- 应避免以根用户身份运行容器
- 应避免使用共享敏感主机命名空间的容器
- 应强制对容器使用最低权限 Linux 功能
- Pod HostPath 卷装载的使用应仅限于已知列表
- 容器应只侦听允许的端口
- 服务应只侦听允许的端口
- 应限制对主机网络和端口的使用
- 应限制替代或禁用容器 AppArmor 配置文件
- 应只从受信任的注册表部署容器映像             

若要详细了解这些建议，请参阅[保护 Kubernetes 工作负载](kubernetes-workload-protections.md)。

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>“应用系统更新”安全控制中的两项建议将被弃用 

**预计更改日期：** 2021 年 2 月

以下两项建议计划在 2021 年 2 月弃用：

- **应重启计算机来应用系统更新**。 这可能会对安全功能分数造成轻微影响。
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议“应在计算机上解决 Log Analytics 代理运行状况问题”。 这可能会对安全功能分数造成轻微影响。

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 数据分类建议的增强

**预计更改日期：** 2021 年第 2 季度

“应用数据分类”安全控制中“应对 SQL 数据库中的敏感数据进行分类”建议的当前版本将被弃用，并替换为更符合 Microsoft 的数据分类策略的新版本 。 因此：

- 该建议将不再影响安全功能分数
- 安全控制（“应用数据分类”）将不再影响安全功能分数
- 建议的 ID 也将更改（当前为 b0df6f56-862d-4730-8597-38c0fd4ebd59）



## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。