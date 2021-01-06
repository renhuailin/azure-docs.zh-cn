---
title: Azure 安全中心的新增功能存档
description: 介绍 Azure 安全中心在六个月以前的新增功能和已更改的功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2020
ms.author: memildin
ms.openlocfilehash: 7325ba1a8a90fec90182f9780c1fb18d29d3c0f1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955257"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能存档

[Azure 安全中心的新增功能](release-notes.md)发行说明主页包含过去六个月的更新，而本页包含更早以前的项目。

本页提供以下方面的信息：

- 新增功能
- Bug 修复
- 已弃用的功能


## <a name="july-2020"></a>2020 年 7 月

7 月的更新包括：
- [虚拟机的漏洞评估现在适用于非市场映像](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure 存储的威胁防护已扩展为包括 Azure 文件存储和 Azure Data Lake Storage Gen2（预览版）](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [启用威胁防护功能的八条新建议](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全性优化 - 注册表扫描和刷新文档速度更快](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [更新了自适应应用程序控制，添加了新建议以及对路径规则中的通配符的支持](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [已弃用六个 SQL 高级数据安全策略](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虚拟机的漏洞评估现在适用于非市场映像

部署漏洞评估解决方案时，安全中心以前会在部署之前执行验证检查。 检查的目的是确认目标虚拟机的市场 SKU。 

从本次更新起，已删除该项检查，你现在可以将漏洞评估工具部署到“自定义”Windows 和 Linux 计算机。 自定义映像是你根据市场默认值修改的映像。

虽然现在可以在更多台计算机上部署集成的漏洞评估扩展（由 Qualys 提供支持），但只有在使用[将集成漏洞扫描程序部署到标准层 VM](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)列出的操作系统时，才可以使用支持

详细了解[虚拟机的集成漏洞扫描程序（需要 Azure Defender）](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。

要详细了解如何使用 Qualys 或 Rapid7 中自己私下许可的漏洞评估解决方案，请参阅[部署合作伙伴漏洞扫描解决方案](deploy-vulnerability-assessment-vm.md)。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure 存储的威胁防护已扩展为包括 Azure 文件存储和 Azure Data Lake Storage Gen2（预览版）

Azure 存储的威胁防护可检测 Azure 存储帐户上的潜在有害活动。 安全中心在检测到对存储帐户的访问或攻击尝试时会显示警报。 

无论数据是存储为 blob 容器、文件共享还是数据湖，都可以为其提供保护。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>启用威胁防护功能的八条新建议

添加了八条新建议，简化了为以下资源类型启用 Azure 安全中心的威胁防护功能的过程：虚拟机、应用服务计划、Azure SQL 数据库服务器、计算机上的 SQL 服务器、Azure 存储帐户、Azure Kubernetes 服务群集、Azure 容器注册表注册表和 Azure Key Vault 保管库。

新建议如下所示：

- **应在 Azure SQL 数据库服务器上启用高级数据安全**
- **应在计算机的 SQL 服务器上启用高级数据安全**
- **应在 Azure 应用服务计划上启用高级威胁防护**
- **应对 Azure 容器注册表的注册表启用高级威胁防护**
- **应对 Azure Key Vault 的保管库启用高级威胁防护**
- **应对 Azure Kubernetes 服务的群集启用高级威胁防护**
- **应对 Azure 存储帐户启用高级威胁防护**
- 应对虚拟机启用高级威胁防护

这些新建议属于“启用 Azure Defender”安全控制。

建议还包括快速修复功能。 

> [!IMPORTANT]
> 修正任一建议都将产生相关资源的保护费用。 如果当前订阅中有相关资源，则立即开始计费。 或者以后在你添加资源时，开始计费。
> 
> 例如，如果订阅中没有任何 Azure Kubernetes 服务群集，并且启用了威胁防护，则不会产生任何费用。 如果以后在同一订阅中添加了群集，它将自动受到保护，并从该时间点开始计费。

有关上述各项的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。

详细了解 [Azure 安全中心的威胁防护](azure-defender.md)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全性优化 - 注册表扫描和刷新文档速度更快

作为对容器安全领域的持续投资的一部分，我们很高兴分享安全中心对 Azure 容器注册表中存储的容器映像的动态扫描方面的显著性能优化。 目前，扫描通常会在大约两分钟内完成。 在某些情况下，可能最多需要 15 分钟。

为更好地说明和指导 Azure 安全中心的容器安全功能，我们还更新了容器安全文档页面。 

若要详细了解安全中心的容器安全，请参阅以下文章：

- [安全中心的容器安全功能概述](container-security.md)
- [与 Azure 容器注册表集成的详细信息](defender-for-container-registries-introduction.md)
- [与 Azure Kubernetes 服务集成的详细信息](defender-for-kubernetes-introduction.md)
- [扫描注册表并强化 Docker 主机的操作说明](container-security.md)
- [威胁防护功能中适用于 Azure Kubernetes 服务群集的安全警报](alerts-reference.md#alerts-akscluster)
- [威胁防护功能中适用于 Azure Kubernetes 服务主机的安全警报](alerts-reference.md#alerts-containerhost)
- [容器的安全建议](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>更新了自适应应用程序控制，添加了新建议以及对路径规则中的通配符的支持

自适应应用程序控制功能已收到两个重要更新：

* 一项新的建议指出以前不允许的可能合法的行为。 “应更新自适应应用程序控制策略中的允许列表规则”这一新建议提示向现有策略添加新规则，以减少自适应应用程序控制违规警报的误报数。

* 路径规则现支持通配符。 在此更新中，可以使用通配符配置允许的路径规则。 支持以下两种方案：

    * 在路径末尾使用通配符允许该文件夹和子文件夹中的所有可执行文件

    * 使用路径中间的通配符启用具有更改的文件夹名称的已知可执行文件名称 (例如，使用已知可执行文件、自动生成的文件夹名称等个人用户文件夹，) 。


[详细了解自适应应用程序控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>已弃用六个 SQL 高级数据安全性策略

即将弃用与 SQL 计算机的高级数据安全性相关的六个策略：

- 应在 SQL 托管实例的“高级数据安全”设置中将“高级威胁保护类型”设置为“全部”
- 应在 SQL Server 的高级数据安全设置中将“高级威胁防护类型”设置为“全部”
- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- 应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”
- 应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知

了解有关[内置策略](./policy-reference.md)的详细信息。



## <a name="june-2020"></a>2020 年 6 月

6 月的更新包括以下内容：
- [安全分数 API（预览）](#secure-score-api-preview)
- [ (Azure、其他云和本地)  (预览版的 SQL 计算机的高级数据安全) ](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [大规模创建连续导出和工作流自动化配置的新策略](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 NSG 保护非面向 Internet 的虚拟机的新建议](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [启用威胁防护和高级数据安全性的新策略](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全功能分数 API（预览）

可以通过[安全功能分数 API](/rest/api/securitycenter/securescores/)（当前处于预览阶段）立即访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，可以使用安全功能分数 API 来获取特定订阅的分数。 此外，还可以使用安全功能分数控件 API 列出安全控件和订阅的当前分数。

有关使用安全功能分数 API 实现的外部工具的示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a> (Azure、其他云和本地)  (预览版的 SQL 计算机的高级数据安全) 

Azure 安全中心的 SQL 计算机高级数据安全现在保护托管在 Azure 中、其他云环境，甚至本地计算机上的 SQL Server。 这会扩展 Azure 原生 SQL Server 的保护，以完全支持混合环境。

高级数据安全可为任何位置的 SQL 计算机提供漏洞评估和高级威胁防护。

设置包含两个步骤：

1. 将 Log Analytics 代理部署到 SQL Server 的主机计算机，以提供与 Azure 帐户的连接。

1. 在安全中心的“定价和设置”页中启用可选捆绑。

详细了解 [SQL 计算机的高级数据安全](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）

添加了两条新建议，以帮助将 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)部署到 Azure Arc 计算机，并确保其受 Azure 安全中心的保护：

- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)
- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)

这些新建议将出现在“应在计算机上安装监视代理”这一现有（相关）建议所在的四个安全控制中：修正安全配置、应用自适应应用程序控制、应用系统更新，以及启用 Endpoint Protection。

建议还包括快速修复功能，以帮助加快部署过程。 

有关这两项新建议的详细信息，请参阅[计算和应用建议](recommendations-reference.md#recs-computeapp)。

若要详细了解 Azure 安全中心如何使用代理，请参阅[什么是 Log Analytics 代理？](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

详细了解 [Azure Arc 计算机的扩展](../azure-arc/servers/manage-vm-extensions.md)。


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>大规模创建连续导出和工作流自动化配置的新策略

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在整个组织中部署自动化配置，请使用以下内置的“DeployIfdNotExist”Azure 策略来创建和配置[连续导出](continuous-export.md)和[工作流自动化](workflow-automation.md)过程：

可在 Azure 策略中找到这些策略：


|目标  |策略  |策略 ID  |
|---------|---------|---------|
|将内容连续导出到事件中心|[为 Azure 安全中心警报和建议部署“导出到事件中心”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|将内容连续导出到 Log Analytics 工作区|[为 Azure 安全中心警报和建议配置“导出到 Log Analytics 工作区”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

请参阅[使用提供的策略大规模地配置工作流自动化](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies)以及[设置连续导出](continuous-export.md#set-up-a-continuous-export)，了解关于如何使用两种导出策略的详细信息。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 NSG 保护非面向 Internet 的虚拟机的新建议

“实现安全最佳做法”安全控制现包括以下新建议：

- **应使用网络安全组来保护非面向 Internet 的虚拟机**

“应使用网络安全组保护面向 Internet 的虚拟机”这一现有建议不区分面向 Internet 的虚拟机和面向非 Internet 的虚拟机。 对于这两种情况，如果未将 VM 分配给网络安全组，则会生成高严重性建议。 这一新建议将区分面向非 Internet 的计算机，以减少误报并避免出现不必要的高严重性警报。

有关详细详细，请参阅[网络建议](recommendations-reference.md#recs-network)表。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>启用威胁防护和高级数据安全性的新策略

以下新策略已添加到 ASC Default 计划，旨在帮助为相关资源类型启用威胁防护或高级数据安全性。

可在 Azure 策略中找到这些策略：


| 策略                                                                                                                                                                                                                                                                | 策略 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [应在 Azure SQL 数据库服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [应在计算机的 SQL 服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [应对 Azure 存储帐户启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [应对 Azure Key Vault 的保管库启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [应在 Azure 应用服务计划上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [应对 Azure 容器注册表的注册表启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [应对 Azure Kubernetes 服务的群集启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [应在虚拟机上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

详细了解 [Azure 安全中心的威胁防护](azure-defender.md)。


## <a name="may-2020"></a>2020 年 5 月

5 月的更新包括以下内容：
- [警报抑制规则（预览版）](#alert-suppression-rules-preview)
- [虚拟机漏洞评估现已正式发布](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [对实时 (JIT) 虚拟机 (VM) 访问权限的更改](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [自定义建议已移至单独的安全控件](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [已添加开关，可在控件中显示建议或以简单列表的形式显示](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [扩展了“实现安全最佳做法”这一安全控件](#expanded-security-control-implement-security-best-practices)
- [具有自定义元数据的自定义策略现已正式发布](#custom-policies-with-custom-metadata-are-now-generally-available)
- [故障转储分析功能正在迁至无文件攻击检测中](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>警报抑制规则（预览版）

这项新功能目前为预览版，它可帮助缓解警报疲劳。 可使用规则来自动隐藏已知无害或已知与你组织中的正常活动相关的警报。 这可让你专注于最相关的威胁。 

仍将生成与你启用的抑制规则相匹配的警报，但它们的状态将设置为“已取消”。 你可在 Azure 门户中查看状态，也可在安全中心查看安全警报。

抑制规则定义了自动取消警报所应遵循的条件。 通常，使用抑制规则来：

- 取消已标识为“误报”的警报

- 取消限制过于频繁地触发而失去作用的警报

详细了解如何[取消来自 Azure 安全中心威胁防护服务的警报](alerts-suppression-rules.md)。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>虚拟机漏洞评估现已正式发布

安全中心的标准层现包含集成的虚拟机漏洞评估，该扩展免费提供。 该扩展由 Qualys 提供支持，但将检测结果直接报告回安全中心。 你无需具备 Qualys 许可证，甚至还不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。

这一新的解决方案可持续扫描你的虚拟机来找出漏洞，并在安全中心显示检测结果。 

若要部署该解决方案，请使用新的安全建议：

“在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）”

详细了解[安全中心集成的虚拟机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>对实时 (JIT) 虚拟机 (VM) 访问权限的更改

安全中心包含一项可选功能，可保护 VM 的管理端口。 这可抵御最常见形式的暴力攻击。

本次更新就此功能进行了以下更改：

- 重命名了推荐你在 VM 上启用 JIT 的建议。 之前称为“应在虚拟机上应用实时网络访问控制”，而现在叫做“应通过即时网络访问控制来保护虚拟机的管理端口”。

- 建议仅在有管理端口打开时才触发。

详细了解 [JIT 访问功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自定义建议已移至单独的安全控件

安全功能分数增强版引入的其中一个安全控制是“实现安全最佳做法”。 为订阅创建的所有自定义建议已自动放入该控件中。 

为便于查找自定义建议，我们已将这些建议移到一个名为“自定义建议”的专用安全控件中。 此控件不会影响你的安全功能分数。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>已添加开关，可在控件中显示建议或以简单列表的形式显示

安全控件是相关安全建议的逻辑组。 它们反映了易受攻击的攻击面。 控件是一组安全建议，附有帮助你实施这些建议的说明。

若要立即查看组织对每个攻击面的保护情况，请查看每个安全控件的分数。

默认情况下，你的建议显示在安全控件中。 通过本次更新，你还可以采用列表形式显示它们。 若要以简单列表的形式查看它们，且列表按受影响的资源的运行状况排序，请使用新的“按控件分组”开关。 开关位于门户中列表的上面。

安全控件及其开关是新的安全功能分数体验的一部分。 请记得在门户中提供反馈。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="的“按控件分组”开关":::

### <a name="expanded-security-control-implement-security-best-practices"></a>扩展了“实现安全最佳做法”这一安全控件 

安全功能分数增强版引入的其中一个安全控制是“实现安全最佳做法”。 如果建议在此控件中显示，则不影响安全功能分数。 

通过本次更新，已将三项建议从它们原先所在的控件移动到这个最佳做法控件中。 我们采取此步骤的原因是我们判定这三项建议的风险比最初设想的要低。

此外，还引入了两项新建议，它们也添加到了此控件中。

移动的三项建议如下：

- **应在对订阅拥有读取权限的帐户上启用 MFA**（原先位于“启用 MFA”控件中）
- **应从订阅中删除具有读取权限的外部帐户**（原先位于“管理访问和权限”控件中）
- **只多只为订阅指定 3 个所有者**（原先位于“管理访问和权限”控件中）

添加到控件中的两项新建议如下：

- **应在 Windows 虚拟机上安装来宾配置扩展（预览版）** - 如果使用 [Azure Policy 来宾配置](../governance/policy/concepts/guest-configuration.md)，则可在虚拟机中查看服务器和应用程序设置（仅限 Windows）。

- **应在计算机上启用 Windows Defender 攻击防护（预览版）** - Windows Defender 攻击防护采用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。

要详细了解 Windows Defender 攻击防护，可参阅[创建和部署攻击防护策略](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

要详细了解安全控件，请参阅[安全功能分数增强版（预览版）](secure-score-security-controls.md)。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自定义元数据的自定义策略现已正式发布

自定义策略现显示在安全中心的建议体验、安全功能分数和法规符合性标准仪表板中。 此功能现已正式发布，可用于在安全中心扩大你组织的安全评估范围。 

在 Azure 策略中创建自定义计划，向该计划添加策略并将它加入 Azure 安全中心，然后将它作为建议直观呈现。

现在，我们还添加了可编辑自定义建议元数据的选项。 元数据选项中有严重级别、修正步骤和威胁信息等。  

详细了解[利用详细信息增强自定义建议](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>故障转储分析功能正在迁至无文件攻击检测中 

我们正在将 Windows 故障转储分析 (CDA) 检测功能集成到[无文件攻击检测](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)中。 无文件攻击检测分析改进了 Windows 计算机的以下安全警报：“发现代码注入”、“检测到伪装 Windows 模块”、“发现 Shellcode”和“检测到可疑的代码段”。

该转换的一些优势如下：

- **主动及时检测恶意软件** - 使用 CDA 方法时，会等到故障发生后再运行分析来查找恶意项目。 使用无文件攻击检测后，可在内存中威胁正在运行时主动识别它们。 

- **警报信息更丰富** - 来自无文件攻击检测的安全警报包含 CDA 中不提供的丰富信息，例如有效网络连接信息。 

- **警报聚合** - CDA 在一个故障转储中检测到多个攻击模式时，会触发多个安全警报。 而无文件攻击检测将从同一进程中确定的所有攻击模式组合到一个警报中，免去了关联多个警报的必要性。

- **降低了对 Log Analytics 工作区的要求** - 包含潜在敏感数据的故障转储将无法上传到 Log Analytics 工作区。






## <a name="april-2020"></a>2020 年 4 月

4 月的更新包括：
- [动态符合性包现已正式发布](#dynamic-compliance-packages-are-now-generally-available)
- [标识建议现包含在 Azure 安全中心的免费层中](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>动态符合性包现已正式发布

Azure 安全中心的法规符合性仪表板现包含动态符合性包（现已正式发布），可跟踪更多行业和法规标准。

可通过安全中心的安全策略页面将动态符合性包添加到订阅或管理组中。 加入标准或基准后，该标准会出现在法规符合性仪表板中，所有关联的符合性数据都映射为评估。 还将提供已加入的所有标准的摘要报表供下载。

现在，你可添加如下标准：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official 和 UK NHS**
- **加拿大联邦 PBMM**
- **Azure CIS 1.1.0（新版）** ，它是 Azure CIS 1.1.0 的完整表示形式

此外，我们还添加了 Azure 安全基准，它是 Microsoft 创作的特定于 Azure 的准则，适合基于常见符合性框架的安全与合规最佳做法。 其他标准一经提供就将在仪表板中受到支持。  
 
详细了解如何[在法规符合性仪表板中自定义一组标准](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>标识建议现包含在 Azure 安全中心的免费层中

Azure 安全中心免费层中针对标识和访问的安全建议现已正式发布。 这是我们努力使云安全状态管理 (CSPM) 功能免费而取得的成果之一。 截至目前，这些建议仅在标准定价层中提供。

标识和访问建议的示例包括：

- “应在对订阅拥有所有者权限的帐户上启用多重身份验证。”
- “最多只能为订阅指定 3 个所有者。”
- “应从订阅中删除弃用的帐户。”

如果你有订阅在免费定价层，则此更改将影响它们的安全功能分数，因为它们之前从未接受过标识和访问安全性评估。

详细了解[标识和访问建议](recommendations-reference.md#recs-identity)。

详细了解[监视标识和访问](security-center-identity-access.md)。



## <a name="march-2020"></a>2020 年 3 月

3 月的更新包括：

- [工作流自动化现已正式发布](#workflow-automation-is-now-generally-available)
- [Azure 安全中心与 Windows Admin Center 的集成](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes 服务保护](#protection-for-azure-kubernetes-service)
- [改进了实时体验](#improved-just-in-time-experience)
- [弃用了两项针对 Web 应用的安全建议](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>工作流自动化现已正式发布

Azure 安全中心现已正式发布工作流自动化功能。 它可用于在安全警报和建议上自动触发逻辑应用。 此外，也可对提供了快速修复选项的警报和所有建议执行手动触发。

每个安全计划都包含事件响应的多个工作流。 这些流程可能包含通知相关利益干系人、启动更改管理进程，以及应用特定的修正步骤。 安全专家建议你尽可能多地将这些流程自动化。 自动化可减少开销，还可确保根据你预定义的要求快速、一致地执行处理步骤，从而增强安全性。

若要详细了解用于运行工作流的自动和手动安全中心功能，请参阅[工作流自动化](workflow-automation.md)。

详细了解如何[创建逻辑应用](../logic-apps/logic-apps-overview.md)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 安全中心与 Windows Admin Center 的集成

现可将本地 Windows 服务器从 Windows Admin Center 直接移动到 Azure 安全中心。 安全中心然后变成你的单一管理界面，可在这里查看 Windows Admin Center 各项资源（包括本地服务器、虚拟机和其他 PaaS 工作负载）的安全信息。

在将服务器从 Windows Admin Center 直接移动到 Azure 安全中心后，你将能够：

- 在 Windows Admin Center 的安全中心扩展中查看安全警报和建议。
- 在 Azure 门户的安全中心内（或通过 API）查看安全状态和检索 Windows Admin Center 托管服务器的其他详细信息。

详细了解[如何将 Azure 安全中心与 Windows Admin Center 集成](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes 服务保护

Azure 安全中心正在扩展其容器安全功能，现可保护 Azure Kubernetes 服务 (AKS)。

常见的开源平台 Kubernetes 已被广泛采用，现在已成为容器业务流程方面的行业标准。 尽管得到了广泛实施，但在如何保护 Kubernetes 环境方面，人们仍然缺少了解。 要抵御容器化应用程序的攻击面，需要具备专业知识来确保基础结构已安全配置且受到持续监视，防范潜在威胁。

安全中心的防范包括：

- **发现和可见性** - 在安全中心内注册的订阅中持续发现托管的 AKS 实例。
- **安全建议** - 帮助你遵守 AKS 安全最佳做法的可操作建议。 这些建议包含在安全功能分数中，确保被视为组织的安全状态的一部分。 你可能会看到的一个与 AKS 相关的建议示例是，“应使用基于角色的访问控制来限制对 Kubernetes 服务群集的访问”。
- **威胁防护** - 通过对 AKS 部署的持续分析，Azure 安全中心会提醒你注意在主机和 AKS 群集级别检测到的威胁和恶意活动。

详细了解 [Azure Kubernetes 服务与安全中心的集成](defender-for-kubernetes-introduction.md)。

详细了解[安全中心内的容器安全功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>改进了实时体验

增强了 Azure 安全中心内保护管理端口的实时工具的功能、操作和 UI，如下所示： 

- **“理由”字段** - 通过 Azure 门户的实时页面请求对虚拟机 (VM) 的访问时，现有一个新的可选字段可用来输入请求原因。 可在活动日志中跟踪在此字段中输入的信息。 
- **自动清除冗余实时 (JIT) 规则** - 无论何时更新 JIT 策略，都会自动运行清理工具来检查整个规则集的有效性。 该工具还将查看你策略中的规则与 NSG 中的规则之间是否存在不匹配的情况。 如果清理工具发现不匹配的情况，它将确定原因，并在确定可安全操作的情况下，删除不再需要的内置规则。 清理工具绝不会删除你创建的规则。 

详细了解 [JIT 访问功能](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>弃用了两项针对 Web 应用的安全建议

即将弃用下面两项与 Web 应用相关的安全建议： 

- 应加强 IaaS NSG 上 Web 应用的规则。
    （相关策略：应该强化 IaaS 上 Web 应用程序的 NSG 规则）

- 应限制对应用服务的访问。
    （相关策略：应限制对应用服务的访问 [预览版]）

这些建议将不再在安全中心的建议列表中显示。 相关策略将不再包含在名为“安全中心默认设置”的计划中。

详细了解[安全建议](recommendations-reference.md)。




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>面向 Linux 的无文件攻击检测（预览版）

随着攻击者越来越多地采用更隐蔽的方法来避免被发现，除了 Windows 之外，Azure 安全中心还扩大了无文件攻击检测范围，现涵盖 Linux。 无文件攻击利用软件漏洞、将恶意有效负载注入良性系统进程，并隐藏在内存中。 这些技术：

- 最大程度地减少或消除了磁盘上恶意软件的痕迹
- 大大降低了基于磁盘的恶意软件扫描解决方案的检测机会

为了应对这种威胁，Azure 安全中心于 2018 年 10 月发布了面向 Windows 的无文件攻击检测，现在还将该检测扩展到了 Linux 上。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>安全功能分数增强版（预览版）

Azure 安全中心的安全功能分数增强版现提供预览版。 在此版本中，多个建议被组合到安全控件中，可更好地反映出你易受攻击的攻击面（例如限制对管理端口的访问）。

请在预览阶段熟悉安全功能分数的更改之处，确定可帮助你进一步保护环境的其他修正措施。

详细了解[安全功能分数增强版（预览版）](secure-score-security-controls.md)。



## <a name="november-2019"></a>2019 年 11 月

11 月的更新包括：
 - [北美区域中的 Azure Key Vault 威胁防护 (预览) ](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [针对 Azure 存储的威胁防护包括恶意软件信誉屏蔽](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [使用逻辑应用实现工作流自动化（预览版）](#workflow-automation-with-logic-apps-preview)
 - [批量资源快速修复功能已推出正式版](#quick-fix-for-bulk-resources-generally-available)
 - [扫描容器映像的漏洞（预览版）](#scan-container-images-for-vulnerabilities-preview)
 - [其他监管合规标准（预览版）](#additional-regulatory-compliance-standards-preview)
 - [针对 Azure Kubernetes 服务的威胁防护（预览版）](#threat-protection-for-azure-kubernetes-service-preview)
 - [虚拟机漏洞评估（预览版）](#virtual-machine-vulnerability-assessment-preview)
 - [Azure 虚拟机上的 SQL 服务器的高级数据安全性（预览版）](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [支持自定义策略（预览版）](#support-for-custom-policies-preview)
 - [在面向社区和合作伙伴的平台中扩大 Azure 安全中心的涵盖范围](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [支持导出建议和警报的高级集成（预览版）](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [从 Windows 管理中心将本地服务器加入安全中心（预览版）](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>针对北美区域 Azure Key Vault 的威胁防护（预览版）

Azure Key Vault 是一个基本服务，它通过提供集中管理云中密钥、机密、加密密钥和策略的功能，来保护数据和提高云应用程序的性能。 由于 Azure Key Vault 存储敏感数据和业务关键数据，因此必须保证密钥保管库及其存储的数据的最高安全性。

Azure 安全中心对 Azure Key Vault 的威胁防护的支持提供额外的安全情报层，用于检测以非寻常和可能有害的方式访问或恶意利用密钥保管库的企图。 此新保护层使得客户无需成为安全专家或管理安全监视系统，就能应对其密钥保管库受到的威胁。 此功能在北美区域推出了公共预览版。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>针对 Azure 存储的威胁防护包括恶意软件信誉屏蔽

针对 Azure 存储的威胁防护提供由 Microsoft 威胁情报支持的新检测，可以使用哈希信誉分析来检测将恶意软件上传到 Azure 存储的行为，并可以检测从活动的 Tor 出口节点（一个匿名代理）进行的可疑访问。 现在，你可以使用 Azure 安全中心查看在各个存储帐户中检测到的恶意软件。


### <a name="workflow-automation-with-logic-apps-preview"></a>使用逻辑应用实现工作流自动化（预览版）

在集中管理安全性和 IT/运营的组织的环境中发现差异时，这些组织可以实施内部工作流程来驱动所需的操作。 在许多情况下，这些工作流是可重复的流程，而自动化可以在组织内部大幅简化流程。

目前我们正在安全中心内引入一项新功能，它可以让客户创建利用 Azure 逻辑应用的自动化配置，并创建可根据具体 ASC 发现结果自动触发这些配置的策略（例如“建议”或“警报”）。 可将 Azure 逻辑应用配置为执行逻辑应用连接器大型社区所支持的任何自定义操作，或使用安全中心提供的某个模板，例如，发送电子邮件或开具 ServiceNow™ 票证。

若要详细了解用于运行工作流的自动和手动安全中心功能，请参阅[工作流自动化](workflow-automation.md)。

若要了解如何创建逻辑应用，请参阅 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>批量资源快速修复功能已推出正式版

由于用户在安全评分中要执行许多任务，有效修正大型机群中的问题可能会变得困难。

为了简化对安全错误配置的修正，快速修正针对批量资源的建议并提高安全评分，请使用“快速修复”修正措施。

此操作可让你选择要对其应用修正措施的资源，并启动一个修正措施来让系统代你配置设置。

现在，安全中心建议页上已向客户推出了快速修复正式版。

在[安全建议参考指南](recommendations-reference.md)中了解哪些建议启用了快速修复。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>扫描容器映像的漏洞（预览版）

Azure 安全中心现在可以扫描 Azure 容器注册表中容器映像的漏洞。

映像扫描的工作原理是分析容器映像文件，然后查看是否存在任何已知漏洞（由 Qualys 提供支持）。

将新容器映像推送到 Azure 容器注册表时，会自动触发扫描本身。 发现的漏洞将以安全中心建议的形式显示，其中包括 Azure 安全评分，以及有关如何修补这些漏洞以减小允许的受攻击面的信息。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他监管合规标准（预览版）

“监管合规”仪表板基于安全中心评估结果提供合规态势的见解。 该仪表板会显示你的环境是否符合特定法规标准和行业基准指定的控制措施与要求，并提供有关如何符合这些要求的规范性建议。

目前，监管合规仪表板支持四项内置标准：Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我们现在宣布推出其他受支持标准的公共预览版：NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大联邦 PBMM、UK Official 和 UK NHS。 此外，我们正在发布 Azure CIS 1.1.0 的更新版本，其中涵盖了标准中的更多控制措施，并增强了扩展性。

[详细了解如何在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>针对 Azure Kubernetes 服务的威胁防护（预览版）

Kubernetes 很快就成了在云中部署和管理软件的新标准。 只有少量的用户对 Kubernetes 拥有丰富的经验；很多用户只是注重一般的工程和管理，而忽略了安全方面。 需要精心配置 Kubernetes 环境以使其保持安全，确保不会公开任何以容器为中心的受攻击面，避免为攻击者打开后门。 安全中心正在将其在容器领域的支持扩展到 Azure 中发展速度最快的服务之一 - Azure Kubernetes 服务 (AKS)。

此公共预览版中的新功能包括：

- **发现和可见性** - 在安全中心的已注册订阅中持续发现 AKS 托管实例。
- **安全分数建议** -可操作的项可帮助客户遵守 AKS 的安全最佳做法，并提高其安全分数。 建议包括诸如 "应使用基于角色的访问控制来限制对 Kubernetes 服务群集的访问权限之类的项目"。
- **威胁检测** - 基于主机和群集的分析，例如“检测到特权容器”。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虚拟机漏洞评估（预览版）

安装在虚拟机中的应用程序可能经常出现漏洞，导致虚拟机遭到入侵。 我们将宣布安全中心标准层包含虚拟机的内置漏洞评估，无额外费用。 由 Qualys 提供支持的漏洞评估公共预览版可让你持续扫描虚拟机上安装的所有应用程序以找出有漏洞的应用程序，并在安全中心门户体验中显示扫描结果。 安全中心负责处理所有部署操作，不需要用户额外付出精力。 接下来，我们计划提供漏洞评估选项来支持客户实现其独特的业务需求。

[详细了解针对 Azure 虚拟机的漏洞评估](deploy-vulnerability-assessment-vm.md)。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虚拟机上的 SQL 服务器的高级数据安全性（预览版）

Azure 安全中心对 IaaS VM 上运行的 SQL 数据库的威胁防护和漏洞评估支持现已推出预览版。

[漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 此服务可让你查看以 Azure 安全评分提供的安全态势，并包含用于解决安全问题和增强数据库防御能力的步骤。

[高级威胁防护](../azure-sql/database/threat-detection-overview.md)检测异常活动，这些活动指示有人企图以非寻常或可能有害的方式访问或恶意利用你的 SQL 服务器。 它会持续监视数据库中的可疑活动，并针对异常的数据库访问模式提供操作导向的安全警报。 这些警报提供可疑活动的详细信息，以及有助于调查和缓解威胁的建议操作。


### <a name="support-for-custom-policies-preview"></a>支持自定义策略（预览版）

Azure 安全中心现在支持自定义策略（预览版）。

我们的客户一直希望在安全中心内扩大其当前安全评估的涵盖范围，以便能够基于他们在 Azure Policy 中创建的策略执行自己的安全评估。 由于支持自定义策略，这种愿望已得到实现。

安全中心建议体验、安全评分和监管合规标准仪表板中将会包含这些新策略。 由于支持自定义策略，现在可以在 Azure Policy 中创建一个自定义计划，然后将其作为策略添加到安全中心，并将其作为建议可视化。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>在面向社区和合作伙伴的平台中扩大 Azure 安全中心的涵盖范围

使用安全中心不仅可以从 Microsoft 接收建议，而且还能从 Check Point、Tenable 和 CyberArk 等合作伙伴提供的现有解决方案以及不断推出的其他许多集成中接收建议。  安全中心的简单加入流程可将你的现有解决方案连接到安全中心，使你可以在一个位置查看安全态势建议、运行统一报告，以及针对内置建议和合作伙伴建议利用安全中心的所有功能。 还可以将安全中心建议导出到合作伙伴产品。

[详细了解 Microsoft 智能安全关联](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>支持导出建议和警报的高级集成（预览版）

若要在安全中心的顶层实现企业级方案，现在可以在除 Azure 门户或 API 以外的其他位置使用安全中心警报和建议。 可直接将这些警报和建议导出到事件中心与 Log Analytics 工作区。 下面是可以围绕这些新功能创建的一些工作流：

- 借助导出到 Log Analytics 工作区的功能，可以使用 Power BI 创建自定义仪表板。
- 借助导出到事件中心的功能，可将安全中心警报和建议导出到第三方 SIEM、实时导出到第三方解决方案，或导出到 Azure 数据资源管理器。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>从 Windows 管理中心将本地服务器加入安全中心（预览版）

Windows 管理中心是一个管理门户，适用于未在 Azure 中部署的 Windows Server，为它们提供多项 Azure 管理功能（例如备份和系统更新）。 我们最近添加了一项功能，允许直接从 Windows 管理中心体验加入这些非 Azure 服务器，使其受 ASC 保护。

使用此新体验，用户可以直接在 Windows 管理中心体验中将 WAC 服务器加入 Azure 安全中心，并查看其安全警报和建议。


## <a name="september-2019"></a>2019 年 9 月

9 月的更新包括：

 - [使用自适应应用程序控制管理规则的功能已改进](#managing-rules-with-adaptive-application-controls-improvements)
 - [使用 Azure Policy 控制容器安全建议](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>使用自适应应用程序控制管理规则的功能已改进

使用自适应应用程序控制管理虚拟机规则的体验已得到改进。 Azure 安全中心的自适应应用程序控制可帮助你控制哪些应用程序能够在你的虚拟机上运行。 除了对规则管理做出了一般性的改进外，在添加新规则时，你还可以通过一个新功能来控制要保护哪些文件类型。

[详细了解自适应应用程序控制](security-center-adaptive-application.md)。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure Policy 控制容器安全建议

现在，可以通过 Azure Policy 启用或禁用 Azure 安全中心提供的有关修正容器安全性中的漏洞的建议。

若要查看已启用的安全策略，请在安全中心内打开“安全策略”页。


## <a name="august-2019"></a>2019 年 8 月

8 月的更新包括：

 - [Azure 防火墙的实时 (JIT) VM 访问](#just-in-time-jit-vm-access-for-azure-firewall)
 - [提升安全态势的一键式修正（预览版）](#single-click-remediation-to-boost-your-security-posture-preview)
 - [跨租户管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火墙的实时 (JIT) VM 访问 

Azure 防火墙的实时 (JIT) VM 访问现已推出正式版。 使用此功能可以确保 Azure 防火墙保护的环境以及 NSG 保护的环境的安全。

JIT VM 访问使用 NSG 和 Azure 防火墙规则，仅在需要时才提供对 VM 的受控访问，以此减少受到网络容量耗尽攻击的风险。

为 VM 启用 JIT 时，可创建一个策略来确定要保护的端口、端口保持打开状态的时间，以及可从哪些已批准的 IP 地址访问这些端口。 此策略可帮助你控制用户在请求访问权限时可执行哪些操作。

请求将记录在 Azure 活动日志中，因此你可以轻松监视和审核访问。 此实时页面还可帮助你快速识别已启用 JIT 的现有 VM，以及建议启用 JIT 的 VM。

[详细了解 Azure 防火墙](../firewall/overview.md)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>提升安全态势的一键式修正（预览版）

安全评分是一个可帮助你评估工作负荷安全状况的工具。 它会评审你的安全建议并确定其优先级，以便你知道要首先执行哪些建议。 这可帮助你找到最严重的安全漏洞，以确定调查优先级。

为了简化安全配置错误的修正并帮助你快速提高安全评分，我们添加了一项新功能，让你一键式修正针对批量资源的建议。

此操作可让你选择要对其应用修正措施的资源，并启动一个修正措施来让系统代你配置设置。

在[安全建议参考指南](recommendations-reference.md)中了解哪些建议启用了快速修复。


### <a name="cross-tenant-management"></a>跨租户管理

安全中心现在可通过 Azure Lighthouse 支持跨租户管理方案。 此功能使你能够在安全中心查看和管理多个租户的安全态势。 

[详细了解跨租户管理体验](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>网络建议的更新

Azure 安全中心 (ASC) 已推出新的网络建议，并改进了一些现有的建议。 现在，使用安全中心可以确保进一步为资源提供更好的网络保护。 

[详细了解网络建议](recommendations-reference.md#recs-network)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>自适应网络强化 - 正式版

公有云中运行的工作负荷面对的最大受攻击面之一是与公共 Internet 之间的连接。 我们的客户发现，他们很难知道要部署哪些网络安全组 (NSG) 规则来确保仅在所需的源范围内提供 Azure 工作负荷。 使用此功能，安全中心可以了解 Azure 工作负荷的网络流量和连接模式，并为面向 Internet 的虚拟机提供 NSG 规则建议。 这有助于我们的客户更好地配置其网络访问策略，并限制受到攻击的风险。 

[详细了解自适应网络强化](security-center-adaptive-network-hardening.md)。