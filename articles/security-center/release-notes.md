---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 06/14/2021
ms.author: memildin
ms.openlocfilehash: 62fb48fb7517f25ceaf2b1e922ece83538157f90
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238039"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。


## <a name="june-2021"></a>2021 年 6 月

6 月的更新包括以下内容：

- [适用于 Key Vault 的 Azure Defender 的新警报](#new-alert-for-azure-defender-for-key-vault)
- [默认禁用使用客户管理的密钥 (CMK) 进行加密的建议](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes 警报的前缀已从“AKS_”更改为“K8S_”](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [弃用了“应用系统更新”安全控制中的两个建议](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>适用于 Key Vault 的 Azure Defender 的新警报

为了扩展 Azure Defender 为 Key Vault 提供的威胁保护，我们添加了以下警报：

| 警报（警报类型）                                                                 | 说明                                                                                                                                                                                                                                                                                                                                                      | MITRE 技巧 | 严重性 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| 可疑 IP 地址对 Key Vault 的访问<br>(KV_SuspiciousIPAccess)  | 被 Microsoft 威胁情报标识为可疑 IP 地址的 IP 已经成功访问了 key vault。 这可能表示基础结构已遭到入侵。 建议进一步调查。 详细了解 [Microsoft 威胁智能功能](https://go.microsoft.com/fwlink/?linkid=2128684)。 | 凭据访问                            | 中型   |
|||

有关详细信息，请参阅：
- [用于 Key Vault 的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
- [响应适用于 Key Vault 的 Azure Defender 警报](defender-for-key-vault-usage.md)
- [适用于 Key Vault 的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>默认禁用使用客户管理的密钥 (CMK) 进行加密的建议

安全中心包含多个建议，它们建议使用客户管理的密钥对静态数据进行加密，例如：

- 容器注册表应使用客户管理的密钥 (CMK) 进行加密
- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密

使用平台管理的密钥自动对 Azure 中的数据进行加密，因此，只有在要求符合组织选择执行的特定政策时，才使用客户管理的密钥。

如此更改后，现已 **默认禁用** 使用 CMK 的建议。 当与你的组织相关时，可以通过将相应安全策略的 *效果* 参数更改为 **AuditIfNotExists** 或 **Enforce** 来启用它们。 有关详细信息，请参阅[启用安全策略](tutorial-security-policy.md#enable-a-security-policy)。

此更改将反映在包含新前缀的建议名称中， **[Enable if required]** ，如以下示例所示：

- [Enable if required] 存储帐户应使用客户管理的密钥来加密静态数据
- [Enable if required] 应使用客户管理的密钥 (CMK) 来加密容器寄存器
- [Enable if required] Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="默认禁用安全中心的 CMK 建议。" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes 警报的前缀已从“AKS_”更改为“K8S_”

Azure Defender for Kubernetes 最近已扩展，可保护托管在本地和多云环境中的 Kubernetes 群集。 参阅[使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)，了解更多信息。

为了反映 Azure Defender for Kubernetes 提供的安全警报不再限于 Azure Kubernetes 服务上的群集，我们已将警报类型的前缀从 “AKS_”更改为“K8S_”。 必要时，还会更新名称和说明。 例如，以下警报：

|警报（警报类型）|说明|
|----|----|
|检测到 Kubernetes 渗透测试工具<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 审核日志分析检测到在 **AKS** 群集中使用了 Kubernetes 渗透测试工具。 此行为可能是合法的，不过，攻击者可能会出于恶意使用此类公用工具。
|||

已更改为：

|警报（警报类型）|说明|
|----|----|
|检测到 Kubernetes 渗透测试工具<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 审核日志分析检测到在 **Kubernetes** 群集中使用了 Kubernetes 渗透测试工具。 此行为可能是合法的，不过，攻击者可能会出于恶意使用此类公用工具。|
|||

将自动转换提到以“AKS_”开始的警报的所有抑制规则。 如果已设置 SIEM 导出或按警报类型引用 Kubernetes 警报的自定义自动化脚本，则需要使用新的警报类型对其进行更新。

有关 Kubernetes 警报的完整列表，请参阅[适用于 Kubernetes 群集的警报](alerts-reference.md#alerts-k8scluster)。

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>弃用了“应用系统更新”安全控制中的两个建议

以下两项建议已被弃用：

- **应为你的云服务角色更新 OS 版本** - 默认情况下，Azure 会定期将来宾 OS 更新为 OS 系列（在服务配置 (.cscfg) 中指定）中支持的最新映像，例如 Windows Server 2016。
- **Kubernetes Services 应升级到不容易受到攻击的 Kubernetes 版本** - 这项建议的评估覆盖面并不像我们所希望的那样广泛。 我们计划使用增强版本替换建议，以更好地满足你的安全需求。


## <a name="may-2021"></a>2021 年 5 月

5 月的更新包括以下内容：

- [适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender 正式发布 (GA)](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [适用于开放源代码关系数据库的 Azure Defender 正式发布 (GA)](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [适用于资源管理器的 Azure Defender 的新警报](#new-alerts-for-azure-defender-for-resource-manager)
- [通过 GitHub 工作流和 Azure Defender (预览版）扫描容器映像的 CI/CD 漏洞](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [可用于某些建议的更多 Resource Graph 查询](#more-resource-graph-queries-available-for-some-recommendations)
- [更改了 SQL 数据分类建议严重性](#sql-data-classification-recommendation-severity-changed)
- [用于启用受信任启动功能（预览版）的新建议](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [用于强化 Kubernetes 群集（预览版）的新建议](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [评估 API 扩充了两个新字段](#assessments-api-expanded-with-two-new-fields)
- [资产盘存增加了云环境筛选器](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender 正式发布 (GA)

这两个云原生广度的威胁保护计划现已正式发布。

这些新的防护极大增强了你在遭到威胁行为体攻击后的复原能力，还大大增加了受 Azure Defender 保护的 Azure 资源数量。

- **适用于资源管理器的 Azure Defender** - 自动监视在你的组织中执行的所有资源管理操作。 有关详细信息，请参阅：
    - [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
    - [响应适用于资源管理器的 Azure Defender 警报](defender-for-resource-manager-usage.md)
    - [适用于资源管理器的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-resourcemanager)

- **适用于 DNS 的 Azure Defender** - 持续监视来自你的 Azure 资源的所有 DNS 查询。 有关详细信息，请参阅：
    - [适用于 DNS 的 Azure Defender 简介](defender-for-dns-introduction.md)
    - [响应适用于 DNS 的 Azure Defender 警报](defender-for-dns-usage.md)
    - [适用于 DNS 的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-dns)

若要简化启用这些计划的过程，请使用建议：

- **应启用 Azure Defender for Resource Manager**
- **应启用 Azure Defender for DNS**

> [!NOTE]
> 启用 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页： https://aka.ms/pricing-security-center 。


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>适用于开放源代码关系数据库的 Azure Defender 正式发布 (GA)

Azure 安全中心使用新的捆绑包扩展其 SQL 保护产品/服务，以涵盖开放源代码关系数据库：

- 适用于 Azure SQL 数据库服务器的 Azure Defender - 保护 Azure 原生 SQL 服务器
- 计算机上的 Azure Defender for SQL 服务器：将相同的保护扩展到混合、多云和本地环境中的 SQL 服务器
- **适用于开放源代码关系数据库的 Azure Defender** - 保护你的Azure Database for MySQL、PostgreSQL 和 MariaDB 单一服务器

适用于开放源代码关系数据库的 Azure Defender 持续监视服务器是否受到安全威胁，并检测异常的数据库活动，指出对 Azure Database for MySQL、PostgreSQL 和 MariaDB 的潜在威胁。 下面是一些示例：

- **精确检测暴力攻击** - 适用于开放源代码关系数据库的 Azure Defender 提供有关尝试的和成功的暴力攻击的详细信息。 让你可以更全面地了解你的环境中的攻击的性质和状态，据此展开调查和进行响应。
- **行为警报检测** - 适用于开放源代码关系数据库的 Azure Defender 会提醒你服务器上存在的可疑和意外行为，例如对数据库的访问模式的更改。
- **基于威胁情报的检测** -Azure Defender 利用 Microsoft 的威胁情报和巨大的知识库来发出威胁警报，以便你进行应对。

阅读[适用于开放源代码关系数据库的 Azure Defender 的简介](defender-for-databases-introduction.md)，了解更多信息。

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>适用于资源管理器的 Azure Defender 的新警报

为了扩展适用于资源管理器的 Azure Defender 提供的威胁保护，我们添加以下提醒：

| 警报（警报类型）                                                                                                                                                | 说明                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE 技巧 | 严重性 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**针对你的 Azure 环境（预览版），以异常方式授予 RBAC 角色的权限**<br>(ARM_AnomalousRBACRoleAssignment)|比较同一个分配者执行的其他分配、同一分配者为相同被分派人执行的其他分配以及在租户中执行的其他分配时，适用于资源管理器的 Azure Defender 检测到异常的 RBAC 角色分配，原因是存在以下异常：分配时间、分配者的地点、分配者、身份验证方法、分配的实体、使用的客户端软件、分配范围。 此操作可能是由你的组织中的合法用户执行的。 或者，这可能表示组织中的某个帐户被入侵，威胁方正在尝试向其拥有的其他用户帐户授予权限。|横向移动，防御规避|中等|
|**以可疑方式为订阅创建的特权自定义角色（预览）**<br>(ARM_PrivilegedRoleDefinitionCreation)|适用于资源管理器的 Azure Defender 检测到你的订阅中发生了可疑的特权自定义角色定义创建行为。 此操作可能是由你的组织中的合法用户执行的。 或者，这可能表明你的组织中的帐户被入侵，并且威胁方正在尝试创建特权角色，以在将来用于规避检测。|横向移动，防御规避|低|
|**来自可疑 IP 地址的 Azure 资源管理器操作（预览版）**<br>(ARM_OperationFromSuspiciousIP)|适用于资源管理器的 Azure Defender 检测到来自某个 IP 地址的操作，这个 IP 地址在威胁情报源中被标记为可疑。|执行|中型|
|**Azure 资源管理器对可疑代理 IP 地址的操作（预览）**<br>(ARM_OperationFromSuspiciousProxyIP)|适用于资源管理器的 Azure Defender 检测到来自某个 IP 地址的资源管理操作，而这个 IP 地址与代理服务（如 TOR）相关联。 虽然这种行为可能是合法的，但经常出现在恶意活动中，在这些恶意活动中，攻击者会试图隐藏其源 IP。|防御规避|中|
||||

有关详细信息，请参阅：
- [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
- [响应适用于资源管理器的 Azure Defender 警报](defender-for-resource-manager-usage.md)
- [适用于资源管理器的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>通过 GitHub 工作流和 Azure Defender (预览版）扫描容器映像的 CI/CD 漏洞

适用于容器注册表的 Azure Defender 现在让 DevSecOps 团队可以观察 GitHub 操作工作流。

针对容器映像的新漏洞扫描功能利用 Trivy 来帮助开发人员在将映像推送到容器注册表 *之前*， 扫描其容器映像中的常见漏洞。

容器扫描报告在 Azure 安全中心中进行汇总，使安全团队能够更好地理解和了解易受攻击的容器映像来源，以及是从哪里生成工作流和存储库。

有关详细信息，请参阅[识别 CI/CD 工作流中有漏洞的容器映像](defender-for-container-registries-cicd.md)。

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>可用于某些建议的更多资源图查询

安全中心的所有建议都可以使用 **打开查询** 中的“Azure 资源图”查看有关受影响的资源的状态信息。 有关此强大功能的完整详细信息，请参阅[在 Azure Resource Graph 资源管理器 (ARG) 中查看建议数据](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)。

安全中心包含内置的漏洞扫描程序，用于扫描 VM、SQL 服务器及其主机，以及容器注册表，以发现安全漏洞。 这些结果将作为建议返回，其中关于每个资源类型的所有单个发现都将集中到一个视图中。 这三个建议是：

- 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
- 应修正虚拟机中的漏洞
- SQL 数据库应已解决漏洞结果
- 计算机上的 SQL 服务器应已解决漏洞结果

进行此更改后，也可以使用 **打开查询** 按钮打开显示安全发现的查询。

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="“打开查询”按钮现提供更详细的查询选项，显示漏洞扫描程序相关建议的安全发现。":::

**打开查询** 按钮还提供了一些其他相关建议的附加选项。

了解关于安全中心漏洞扫描程序的更多信息：

- [适用于 Azure 和混合计算机的 Azure Defender 集成式漏洞评估扫描程序](deploy-vulnerability-assessment-vm.md)
- [适用于 SQL 服务器的集成式漏洞评估扫描程序](defender-for-sql-on-machines-vulnerability-assessment.md)
- [适用于容器寄存表的 Azure Defender 集成式漏洞评估扫描程序](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>更改了 SQL 数据分类建议严重性

已将 **“应对 SQL 数据库中的敏感数据进行分类”** 建议的严重性从 **高** 更改为 **低**。

这是对 [SQL 数据分类建议的增强](upcoming-changes.md#enhancements-to-sql-data-classification-recommendation)中公布的此建议的更改的一部分。 


### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>用于启用受信任启动功能（预览版）的新建议

Azure 提供受信任启动作为一种无缝提高[第 2 代](../virtual-machines/generation-2.md) VM 安全性的方式。 受信任启动能够防范具有持续性的高级攻击手法。 受信任启动由多种可单独启用的协调式基础结构技术组成。 每种技术都针对错综复杂的威胁提供另一层防御。 在 [Azure 虚拟机的受信任启动](../virtual-machines/trusted-launch.md)中了解详细信息。

> [!IMPORTANT]
> 受信任启动要求创建新的虚拟机。 如果现有的虚拟机在最初创建时未配置受信任启动，则无法在其上启用受信任启动。
> 
> 受信任启动目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

安全中心的建议 **应在受支持的虚拟机上启用 vTPM** 确保你的 Azure VM 会使用 vTPM。 硬件受信任的平台模块的这个虚拟化版本通过测量 VM（UEFI、OS、系统和驱动程序）的整个启动链来实现证明。

启用 vTPM 后， **来宾证明扩展名** 可以远程验证安全启动。 以下建议可确保部署此扩展名：

- **应在受支持的 Windows 虚拟机上启用安全启动**
- **应在受支持的 Windows 虚拟机上安装来宾证明扩展名**
- **应在受支持的 Windows 虚拟机规模集上安装来宾证明扩展名**
- **应在受支持的 Linux 虚拟机上安装来宾证明扩展名**
- **应在受支持的 Linux 虚拟机规模集上安装来宾证明扩展名**

在 [Azure 虚拟机的受信任启动](../virtual-machines/trusted-launch.md)中了解详细信息。 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>用于强化 Kubernetes 群集（预览版）的新建议

以下建议可用于进一步强化 Kubernetes 群集

- **Kubernetes 群集不得使用默认命名空间** - 防止在 Kubernetes 群集中使用默认命名空间，以防止对 ConfigMap、Pod、Secret、Service 和 ServiceAccount 资源类型进行未经授权的访问。
- **Kubernetes 群集应禁用自动装载 API 凭据** - 若要防止可能泄露的 Pod 资源对 Kubernetes 群集运行 API 命令，请禁用自动装载 API 凭据。
- **Kubernetes 群集不应授予 CAPSYSADMIN 安全功能**

如需了解安全中心如何保护容器化环境，请查阅[安全中心的容器安全性](container-security.md)。

### <a name="assessments-api-expanded-with-two-new-fields"></a>评估扩充了两个新字段的  API

我们已将以下两个字段添加到[评估 REST API](/rest/api/securitycenter/assessments)中：

- **FirstEvaluationDate** – 创建和首次评估建议的时间。 返回为 ISO 8601 格式的 UTC 时间。
- **StatusChangeDate** – 建议状态上次更改的时间。 返回为 ISO 8601 格式的 UTC 时间。

这些字段的初始默认值（对于所有建议）为 `2021-03-14T00:00:00+0000000Z`。

若要访问此信息，可以使用下表中的任意方法。

| 工具                 | 详细信息                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API 调用        | 获取 https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| 连续导出    | 这两个专用字段将可用于 Log Analytics 工作区数据                                                                                            |
| [CSV 导出](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | CSV 文件中包含这两个字段                                                        |
|                      |                                                                                                                                                                        |


详细了解[评估 REST API](/rest/api/securitycenter/assessments)。


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>资产盘存增加了云环境筛选器

安全中心的资产盘存页提供多个筛选器，可快速优化显示的资源列表。 有关详细信息，请参阅[利用资产清单浏览和管理资源](asset-inventory.md)。

新筛选器提供根据使用安全中心的多云功能连接的云帐户来优化列表的选项：

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="盘存环境筛选器":::

详细了解多云功能：

- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>2021 年 4 月

4 月的更新包括：
- [刷新了资源运行状况页（预览版）](#refreshed-resource-health-page-in-preview)
- [现在会每周重新扫描最近拉取的容器注册表映像（正式公布 (GA)）](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（正式发布 (GA)）](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [与来宾配置相关的四个新建议（预览版）](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK 建议移动到最佳做法安全控制](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender 警报已弃用](#11-azure-defender-alerts-deprecated)
- [“应用系统更新”安全控制中的两个建议已弃用](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 条建议在不同安全控件之间进行了调动](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>刷新了资源运行状况页（预览版）

安全中心的资源运行状况已展开、增强和改进，提供单个资源总体运行状况的快照视图。 

可以查看有关该资源的详细信息以及适用于该资源的所有建议。 如果使用 [Azure Defender](azure-defender.md)，还可查看该特定资源的未解决的安全警报。

若要打开资源的资源运行状况页，请从[资产清单页](asset-inventory.md)中选择任何资源。

安全中心门户页面中的此预览页显示：

1. **资源信息**：资源组及其附加到的订阅，以及地理位置等。
1. **应用的安全功能**：是否为资源启用 Azure Defender。
1. **未完成的建议和警报数**：未完成的安全建议和 Azure Defender 警报的数量。
1. **可操作的建议和警报**：两个选项卡列出适用于资源的建议和警报。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="显示虚拟机运行状况信息的 Azure 安全中心的资源运行状况页":::

参阅[教程：调查资源的运行状况](investigate-resource-health.md)，了解更多信息。


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>现在会每周重新扫描最近拉取的容器注册表映像（正式公布 (GA)）

适用于容器注册表的 Azure Defender 包含内置漏洞扫描程序。 此扫描程序会立即扫描推送到注册表中的任何映像以及在过去 30 天内拉取的任何映像。

每天都会发现新漏洞。 通过此更新，将每周重新扫描一次过去 30 天内从注册表拉取的容器映像。 这可确保在映像中识别新发现的漏洞。

扫描按映像收费，因此重新扫描不产生额外费用。

可在[使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞](defender-for-container-registries-usage.md)中详细了解此扫描程序。


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）

Azure Defender for Kubernetes 正在扩展其威胁防护功能，以便在部署时保护其群集。 这已通过集成[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 及其新的[扩展功能](../azure-arc/kubernetes/extensions.md)来实现。 

在非 Azure Kubernetes 群集上启用 Azure Arc 后，根据 Azure 安全中心提供的新建议，仅需单击几下，即可将 Azure Defender 扩展部署到其中。

使用建议（启用 Azure Arc 的 Kubernetes 群集应已安装 Azure Defender 的扩展）和扩展保护在其他云提供程序中部署的 Kubernetes 群集，但不支持其托管的 Kubernetes 服务。

Azure 安全中心、Azure Defender 和启用了 Azure Arc 的 Kubernetes 之间的集成将带来以下影响：

- 轻松地将 Azure Defender 扩展预配到未受保护的 Azure Arc 启用 Kubernetes 群集（手动和大规模）
- 从 Azure Arc 门户监视 Azure Defender 扩展及其预配状态
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的安全中心安全建议
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的 Azure Defender 标识安全威胁
- 启用 Azure Arc 的 Kubernetes 群集可以集成到 Azure 安全中心平台和体验

有关详细信息，[请参阅通过本地和多云 Kubernetes 群集使用 Azure Defender for Kubernetes](defender-for-kubernetes-azure-arc.md)。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="有关为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展的 Azure 安全中心建议。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Microsoft Defender for Endpoint 与 Azure Defender 现在支持 Windows Server 2019 和 Windows 10 Virtual Desktop (WVD)（正式公布 (GA)）

Microsoft Defender for Endpoint 是一种整体的、云交付的终结点安全解决方案。 它提供基于风险的漏洞管理和评估以及终结点检测和响应 (EDR)。 有关将 Defender for Endpoint 与 Azure 安全中心一起使用的好处的完整列表，请参阅[使用安全中心的集成式 EDR 解决方案 (Microsoft Defender for Endpoint) 保护终结点](security-center-wdatp.md)。

为 Windows Server 上的服务器启用 Azure Defender 时，Defender for Endpoint 的许可证会包含在计划在中。 如果已为服务器启用 Azure Defender 并且订阅中有 Windows 2019 服务器，则它们会自动接收包含此更新的 Defender for Endpoint。 无需手动操作。 

支持现在已扩展为包括 Windows Server 2019 和 [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)。

> [!NOTE]
> 如果要在 Windows Server 2019 计算机上启用 Defender for Endpoint，请确保它满足[启用 Microsoft Defender for Endpoint 集成](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)中所述的先决条件。


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）

添加了两项新建议，以简化启用[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于 DNS 的 Azure Defender](defender-for-dns-introduction.md)：

- **应启用适用于资源管理器的 Azure Defender** - 适用于资源管理器的 Defender 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。
- **应启用适用于 DNS 的 Azure Defender** - 适用于 DNS 的 Defender 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。

启用 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页： https://aka.ms/pricing-security-center 。

> [!TIP]
> 预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM

我们添加了适用于 Azure 安全中心的三个标准。 使用监管合规性仪表板，现在可以根据以下标准来跟踪合规性：

- [CIS Microsoft Azure 基础基准检验 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC 级别 3](../governance/policy/samples/cmmc-l3.md)
- [受限于新西兰 ISM](../governance/policy/samples/new-zealand-ism.md)

可以按照[在监管合规性仪表板中自定义标准集](update-regulatory-compliance-packages.md)中所述，将这些标准分配给订阅。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="添加了三个标准，用于 Azure 安全中心的监管合规性仪表板。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

了解详细信息，请参阅：
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)
- [教程：提高合规性](security-center-compliance-dashboard.md)
- [常见问题解答 - 法规合规性仪表板](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>与来宾配置相关的四个新建议（预览版）

Azure 的[来宾配置扩展](../governance/policy/concepts/guest-configuration.md) 向安全中心报告，以帮助确保强化虚拟机的来宾内设置。 已启用 Arc 的服务器不需要该扩展，因为已连接 Arc 的计算机代理中包含该扩展。 扩展需要计算机上系统托管标识。

我们已将四个新建议添加到安全中心，以便充分利用此扩展。

- 这两个建议会提示安装该扩展及其所需的系统托管标识：
    - **应在计算机上安装来宾配置扩展**
    - **应使用系统分配的托管标识来部署虚拟机的来宾配置扩展**

- 当扩展已安装并正在运行时，它将开始审核你的计算机，此时系统将提示你强化设置，例如配置操作系统和环境设置。 这两个建议将提示你按如下所述强化 Windows 和 Linux 计算机：
    - **应在计算机上启用 Windows Defender 攻击防护**
    - **对 Linux 虚拟机进行身份验证需要 SSH 密钥**

详细[了解 Azure Policy 的来宾配置](../governance/policy/concepts/guest-configuration.md)。

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK 建议移动到最佳做法安全控制

每个组织的安全计划都包含数据加密要求。 默认情况下，通过服务托管密钥对 Azure 客户的数据进行静态加密。 但是，客户管理的密钥 (CMK) 通常需要满足法规符合性标准。 通过 CMK 能够使用自己创建并拥有的 [Azure Key Vault](../key-vault/general/overview.md) 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。

Azure 安全中心的安全控制是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 “实现安全最佳做法”这一安全控制得分为零。 因此，此控制中的建议不会影响安全评分。

下面列出的建议将移到“实现安全最佳做法”这一安全控制，以更好地反应它们的可选性质。 这一移动确保了这些建议都处于最适当的控制之下，以满足其目标。

- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密
- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密
- 容器注册表应使用客户管理的密钥 (CMK) 进行加密
- SQL 托管实例应使用客户管理的密钥进行静态数据加密
- SQL Server 应使用客户管理的密钥进行静态数据加密
- 存储帐户应使用客户管理的密钥 (CMK) 进行加密

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender 警报已弃用

下面列出的 11 个 Azure Defender 警报已弃用。

- 新警报将取代下面两种警报并扩大覆盖范围：

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 预览版 - 检测到 MicroBurst 工具包“Get-AzureDomainInfo”函数运行 |
    | ARM_MicroBurstRunbook    | 预览版 - 检测到 MicroBurst 工具包“Get-AzurePasswords”函数运行  |
    |                          |                                                                          |

- 下面 9 种警报与已弃用的 Azure Active Directory 标识保护连接器 (IPC) 相关：

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
 
    > [!TIP]
    > 这 9 种 IPC 警报绝不是安全中心警报。 它们是 Azure Active Directory (AAD) 标识保护连接器 (IPC) 的一部分，此连接器将这些警警报发送给安全中心。 在过去 2 年中，只看到这些警报的客户就是在 2019 或更早版本中配置了导出（从连接器到 ASC）。 AAD IPC 继续将其显示其专属的警报系统中，并在 Azure Sentinel 中继续提供。 唯一的变化是它们不再出现在安全中心。

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>“应用系统更新”安全控制中的两项建议已弃用 

下面这两个建议已弃用，这些更改可能会对安全分数产生轻微影响：

- 应重启计算机来应用系统更新
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议，即“应在计算机上解决 Log Analytics 代理运行状况问题”

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴

Azure Defender 仪表板的覆盖区包括对应于环境相关 Azure Defender 计划的磁贴。 由于报告受保护和未受保护资源数量时出现问题，我们决定在解决此问题前暂时删除适用于计算机上的 SQL 的 Azure Defender 的资源覆盖状态。


### <a name="21-recommendations-moved-between-security-controls"></a>21 条建议在不同安全控件之间进行了调动 

以下建议已移动到其他的安全控件。 安全控件是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 这一调动确保了每个建议都处于最适当的控制之下，以满足其目标。

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。

|建议 |更改和影响  |
|---------|---------|
|应对 SQL Server 启用漏洞评估<br>应对 SQL 托管实例启用漏洞评估<br>应立即修正 SQL 数据库的漏洞<br>应修正 VM 中的 SQL 数据库漏洞     |从“修正漏洞”（得 6 分）<br>变为“修正安全配置“（得 4 分）。<br>根据你的环境，这些建议会减少对评分的影响。|
|应该为你的订阅分配了多个所有者<br>自动化帐户变量应进行加密<br> IoT 设备 - 经审核的进程已停止发送事件<br> IoT 设备 - 操作系统基线验证失败<br> IoT 设备 - 需要进行 TLS 加密套件升级<br> IoT 设备 - 打开设备上的端口<br> IoT 设备 - 在其中一个链中找到了宽容防火墙策略<br> IoT 设备 - 在输入链中找到了宽容防火墙规则<br> IoT 设备 - 在输出链中找到了宽容防火墙规则<br>应启用 IoT 中心的诊断日志<br> IoT 设备 - 代理正在发送未充分利用的消息<br>IoT 设备 - 默认 IP 筛选策略应为“拒绝”<br>IoT 设备 - IP 筛选器规则为“大范围 IP”<br>IoT 设备 - 应调整代理消息间隔和大小<br>IoT 设备 - 完全相同的身份验证凭据<br>IoT 设备 - 经审核的进程停止发送事件<br>IoT 设备 - 应修复操作系统 (OS) 基线配置|移到 **实施安全最佳做法**。<br>如果某条建议移到“实施安全最佳做法”控制（不得分），则这条建议不再影响安全分数。|
|||


## <a name="march-2021"></a>2021 年 3 月

3 月的更新包括：

- [集成到安全中心的 Azure 防火墙管理](#azure-firewall-management-integrated-into-security-center)
- [SQL 漏洞评估现在包含“禁用规则”体验（预览）](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [法规合规性仪表板现在包含 Azure 审核报告（预览）](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [更新部署工作流自动化的的策略](#updates-to-the-policies-for-deploying-workflow-automation)
- [两条旧版建议不再将数据直接写入 Azure 活动日志](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [建议页面功能增强](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>集成到安全中心的 Azure 防火墙管理

打开 Azure 安全中心时，首先显示的是概述页面。 

此交互式仪表板提供了混合云工作负载安全状况的统一视图。 此外，它还显示安全警报、覆盖范围信息等等。

在帮助你通过中心体验查看安全状态的过程中，我们已将 Azure 防火墙管理器集成到此仪表板中。 你现在可以检查所有网络的防火墙覆盖状态，并从安全中心开始集中管理 Azure 防火墙策略。

有关此仪表板的详细信息，请参阅 [Azure 安全中心的概述页面](overview-page.md)。

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="安全中心的概述仪表板，其中包含 Azure 防火墙的磁贴":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL 漏洞评估现在包含“禁用规则”体验（预览）

安全中心包含内置漏洞扫描仪，有助于发现、跟踪和修正潜在的数据库漏洞。 评估扫描结果概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

有关详细信息，请参阅[禁用特定发现结果](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)。



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板

在 Ignite Spring 2021 召开过程中，我们宣布在安全中心中推出集成式 Azure Monitor 工作簿。

可以利用新集成开始使用安全中心的库中的现成模板。 通过使用工作簿模板，可以访问并生成动态、可视化报告，以跟踪组织的安全状况。 此外，还可以根据安全中心数据或任何其他受支持的数据类型创建新的工作簿，并且快速部署来自安全中心 GitHub 社区的社区工作簿。

提供了三个模板报告：

- 一段时间内的安全功能分数 - 跟踪订阅的分数以及对资源建议的更改
- 系统更新 - 按资源、OS 和严重性等查看缺失的系统更新
- 漏洞评估发现结果 - 查看对 Azure 资源进行漏洞扫描的发现结果

如需了解如何使用这些报告或生成自己的报告，请参阅[创建安全中心数据的丰富的交互式报告](custom-dashboards-azure-workbooks.md)。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="一段时间内的安全功能分数报告。":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>法规合规性仪表板现在包含 Azure 审核报告（预览）

现在可以从法规合规性仪表板的工具栏下载 Azure 和 Dynamics 认证报告。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="法规合规性仪表板的工具栏":::

可以选择用于相关报告类型（PCI、SOC 和 ISO 等）的选项卡，然后使用筛选器来查找所需的特定报告。

有关详细信息，请参阅[管理法规合规性仪表板中的标准](update-regulatory-compliance-packages.md)。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="筛选可用 Azure 审核报告的列表。":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据

建议详细信息页现在包含“在 ARG 中浏览”工具栏按钮。 请使用此按钮来打开 Azure Resource Graph 查询，并浏览、导出和共享建议的数据。

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

详细了解 [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)。

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="在 Azure Resource Graph 中浏览建议数据。":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>对部署工作流自动化的的策略的更新

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

我们提供三个 Azure Policy“DeployIfNotExist”策略，这些策略可以创建并配置工作流自动化过程，以便你可以在组织内部署自动化：

|目标  |策略  |策略 ID  |
|---------|---------|---------|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|用于法规合规性的工作流自动化发生更改|[部署 Azure 安全中心合规工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

以下是对这些策略的功能进行的两项更新：

- 分配后，它们将通过强制执行保持启用状态。
- 现在可以自定义这些策略，并更新任意参数，即使它们已部署，也是如此。 例如，如果用户想添加另一个评估密钥或编辑现有的评估密钥，则可以执行此操作。

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

了解如何[自动响应安全中心触发器](workflow-automation.md)。


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>两条旧版建议不再将数据直接写入 Azure 活动日志 

安全中心将几乎所有安全建议的数据传递到 Azure 顾问，后者又将数据写入 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

对于其中两条建议，数据将同时直接写入 Azure 活动日志。 通过这项更改，安全中心会停止将这些旧版安全建议的数据直接写入活动日志。 而我们要将数据导出到 Azure 顾问，就像我们针对所有其他建议所做的那样。

这两条旧版建议为：
- 应在计算机上解决 Endpoint Protection 运行状况问题
- 应该修复计算机上安全配置中的漏洞

如果你一直是在活动日志的“TaskDiscovery 类型的建议”类别中访问这两条建议的信息，现在不再可以这样操作。


### <a name="recommendations-page-enhancements"></a>建议页面功能增强 

我们发布了改进版本的建议列表，以便直观地显示更多信息。

此页面现在显示：

1. 每个安全控制措施的最高分数和当前分数。
1. 图标（代替标记），如“修复”和“预览” 。
1. 一个新列，显示与每条建议相关的[策略计划](security-policy-concept.md)，在禁用“按控制措施分组”时显示。

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure 安全中心的建议页的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure 安全中心的建议“平面”列表的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。


## <a name="february-2021"></a>2021 年 2 月

2 月的更新包括：

- [Azure 门户中的新安全警报页发布了正式版 (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes 工作负载保护建议发布了正式版 (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（预览）](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [直接链接到建议详细信息页中的策略](#direct-link-to-policy-from-recommendation-details-page)
- [SQL 数据分类建议不再影响安全功能分数](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [工作流自动化可以由对法规合规性评估的更改触发（预览）](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [资产清单页增强功能](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Azure 门户中的新安全警报页发布了正式版 (GA)

Azure 安全中心的安全警报页经过重新设计，可提供以下内容：

- 更好的警报会审体验 - 列表包含可自定义的筛选器和分组选项，有助于减少警报疲劳，让你能够更轻松地专注于相关度最高的威胁。
- 警报列表中包含更多信息 - 例如 MITRE ATT&ACK 策略。
- 用于创建示例警报的按钮 - 要评估 Azure Defender 功能并测试警报 配置（对于 SIEM 集成、电子邮件通知和工作流自动化），可以从所有 Azure Defender 计划创建示例警报。
- 与 Azure Sentinel 事件体验的一致性 - 对于同时使用这两种产品的客户，现在可以更直接地在它们之间进行切换。
- 更好的大型警报列表性能。
- 警报列表键盘导航。
- **来自 Azure Resource Graph 的警报** - 可以在 Azure Resource Graph 中查询警报，它是适用于所有资源的类 Kusto API。 如果要构建自己的警报仪表板，这也很有用。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。
- 创建示例警报功能 - 若要通过新的警报体验创建示例警报，请参阅[生成 Azure Defender 示例警报](security-center-alert-validation.md#generate-sample-azure-defender-alerts)。

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure 安全中心的安全警报列表":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes 工作负载保护建议发布了正式版 (GA)

我们很高兴地宣布，已正式发布一组针对 Kubernetes 工作负载保护的建议。

为了确保 Kubernetes 工作负载在默认情况下是安全的，安全中心添加了 Kubernetes 级别的强化建议，其中包括具有 Kubernetes 准入控制的执行选项。

在 Azure Kubernetes 服务 (AKS) 群集上安装适用于 Kubernetes 的 Azure Policy 加载项后，将按照预先定义的一组最佳做法（显示为 13 条安全建议）监视对 Kubernetes API 服务器的每个请求，然后再将其保存到群集。 然后，可以配置为强制实施最佳做法，并规定将其用于未来的工作负载。

例如，可以规定不应创建特权容器，并且阻止以后的任何请求。

有关详细信息，请参阅[使用 Kubernetes 准入控制实现工作负载保护最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)。

> [!NOTE]
> 虽然建议之前为预览版，但它们当时未显示 AKS 群集资源运行不正常，而且在计算安全功能分数时没有纳入这些建议。 发布此 GA 后，计算分数时将纳入这些建议。 如果尚未对其进行修正，则可能会对安全功能分数造成轻微影响。 请尽量进行修正，具体请参阅[修正 Azure 安全中心内的建议](security-center-remediate-recommendations.md)。


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD) （预览）

Microsoft Defender for Endpoint 是一种整体的、云交付的终结点安全解决方案。 它提供基于风险的漏洞管理和评估以及终结点检测和响应 (EDR)。 有关将 Defender for Endpoint 与 Azure 安全中心一起使用的好处的完整列表，请参阅[使用安全中心的集成式 EDR 解决方案 (Microsoft Defender for Endpoint) 保护终结点](security-center-wdatp.md)。

为 Windows Server 上的服务器启用 Azure Defender 时，Defender for Endpoint 的许可证会包含在计划在中。 如果已为服务器启用 Azure Defender 并且订阅中有 Windows 2019 服务器，则它们会自动接收包含此更新的 Defender for Endpoint。 无需手动操作。 

支持现在已扩展为包括 Windows Server 2019 和 [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)。

> [!NOTE]
> 如果要在 Windows Server 2019 计算机上启用 Defender for Endpoint，请确保它满足[启用 Microsoft Defender for Endpoint 集成](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)中所述的先决条件。

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>直接链接到建议详细信息页中的策略

查看建议的详细信息时，能够查看基础策略通常会很有帮助。 对于策略支持的每条建议，建议详细信息页面上都有一个新链接：

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="链接到 Azure Policy 页面，了解支持建议的具体策略。":::

使用此链接可查看策略定义和计算逻辑。 

如果查看[安全建议参考指南](recommendations-reference.md)上的建议列表，你还将看到指向策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问 Azure Policy 页面来了解具体策略。" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL 数据分类建议不再影响安全功能分数
“应对 SQL 数据库中的敏感数据进行分类”建议不再影响安全功能分数。 这是“应用数据分类”安全控件中唯一的建议，因此该控件目前的安全功能分数值为 0。

有关安全中心中所有安全控件的完整列表及其分数和每个安全控件中的建议列表，请参阅[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)。

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>工作流自动化可以由对法规合规性评估的更改触发（预览）
我们向工作流自动化的触发器选项添加了第三种数据类型：对监管合规性评估的更改。

了解如何使用[自动响应安全中心触发器](workflow-automation.md)中的工作流自动化工具。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>资产清单页增强功能
安全中心的资产清单页在以下方面有所改进：

- 页面顶部的摘要现在包括“未注册的订阅”，显示未启用安全中心的订阅数。

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="资产清单页面顶部摘要中未注册订阅的计数。":::

- 筛选器进行了扩展和增强，包括：
    - **计数** - 每个筛选器都会显示满足每一类条件的资源数

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure 安全中心“资产清单”页的筛选器中的计数。":::

    - **包含豁免筛选器**（可选）- 将结果范围缩小为有/没有豁免的资源。 默认情况下不会显示此筛选器，但可从 **添加筛选器** 按钮进行访问。

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="在 Azure 安全中心的“资产清单”页中添加筛选器“包含豁免”":::

详细了解如何[利用资产清单浏览和管理资源](asset-inventory.md)。

## <a name="january-2021"></a>2021 年 1 月

一月的更新包括：

- [Azure 安全基准现在是 Azure 安全中心的默认策略计划](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [本地和多云计算机的漏洞评估已发布正式发布版 (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [预览版中现可提供管理组的安全分数](#secure-score-for-management-groups-is-now-available-in-preview)
- [安全分数 API 已发布正式发布版 (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [用于应用服务的 Azure Defender 添加了无关联的 DNS 保护](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [多云连接器发布了正式发布版 (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [从订阅和管理组的安全分数中免除所有建议](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [用户现在可以向其全局管理员请求租户范围内的可见性](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [将经筛选的建议列表导出为 CSV](#csv-export-of-filtered-list-of-recommendations)
- [在 Azure Policy 评估中，“不适用”资源现报告为“合规”](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [通过连续导出（预览版）导出安全分数和法规合规性数据的每周快照](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure 安全基准现在是 Azure 安全中心的默认策略计划

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。

最近几个月，安全中心的内置安全建议列表已显著增加，从而扩大了此基准的覆盖范围。

从此版本开始，该基准是安全中心建议的基础，并且已完全集成为默认策略计划。 

所有 Azure 服务文档中都有一个安全基线页面。 例如，[这是安全中心的基线](security-baseline.md)。 这些基线均基于 Azure 安全基准。

如果使用的是安全中心的法规符合性仪表板，则你在过渡期间会看到两个基准实例：

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure 安全中心的法规符合性仪表板显示 Azure 安全基准":::

现有建议不受影响，并且随着基准的增加，安全中心内将自动反映出这些更改。 

若要了解详细信息，请参阅以下页面：

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>本地和多云计算机的漏洞评估已发布正式发布版 (GA)

10 月，我们随[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 的集成式漏洞评估扫描器（由 Qualys 提供支持）提供了扫描已启用 Azure Arc 的服务器的预览。

现已发布正式发布版 (GA)。

当你在非 Azure 计算机上启用了 Azure Arc 后，安全中心将提供两种向计算机部署集成式漏洞扫描器的选项（手动和大规模）。

此次更新后，你便可以发掘 Azure Defender 的强大功能，合并所有 Azure 和非 Azure 资产的漏洞管理计划。

主要功能：

- 监视 Azure Arc 计算机上的 VA（漏洞评估）扫描器预配状态
- 将集成式 VA 代理预配到未受保护的 Windows 和 Linux Azure Arc 计算机（手动或大规模）
- 从部署的代理接收和分析检测到的漏洞（手动和大规模）
- 统一的 Azure VM 和 Azure Arc 计算机体验

[详细了解如何将集成式漏洞扫描器部署到混合计算机](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[详细了解启用了 Azure Arc 的服务器](../azure-arc/servers/index.yml)。


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>预览版中现可提供管理组的安全分数

除了订阅级别外，“安全分数”页面现在还会显示管理组的汇总安全分数。 因此，现在可以查看组织中管理组的列表以及每个管理组的分数。

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="正在查看管理组的安全分数。":::

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>安全分数 API 已发布正式发布版 (GA)

现在可以通过[安全分数 API](/rest/api/securitycenter/securescores/) 访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如：

- 使用安全功能分数 API 获取特定订阅的分数
- 使用安全功能分数控件 API 列出安全控件和订阅的当前分数

若要了解可通过安全功能分数 API 实现的外部工具，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>用于应用服务的 Azure Defender 添加了无关联的 DNS 保护

子域接管是组织常见的严重威胁。 当拥有指向已撤销的网站的 DNS 记录时，可能会发生子域接管。 这类 DNS 记录也称为“无关联的 DNS”项。 CNAME 记录特别容易受到此威胁的攻击。 

通过子域接管，威胁参与者可以将要流向组织的域的流量重定向到执行恶意活动的站点。

现在，当对应用服务网站解除授权时，用于应用服务的 Azure Defender 会检测到无关联的 DNS 条目。 此时，DNS 条目指向不存在的资源，并且网站容易受到子域接管攻击。 无论你的域是由 Azure DNS 托管还是由外部域注册器托管，都可使用这些保护；这些保护既适用于 Windows 上的应用服务，也适用于 Linux 上的应用服务。

了解详细信息：

- [应用服务警报引用表](alerts-reference.md#alerts-azureappserv) - 包括两个新的 Azure Defender 警报，它们在检测到无关联的 DNS 条目时触发
- [防止无关联的 DNS 条目并避免子域接管](../security/fundamentals/subdomain-takeover.md) - 了解子域接管威胁和无关联的 DNS 方面
- [用于应用服务的 Azure Defender 简介](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>多云连接器发布了正式发布版 (GA)

由于云工作负载通常跨多个云平台分布，因此云安全服务也需要如此。

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

连接 AWS 或 GCP 帐户会将其本机安全工具（如 AWS 安全中心和 GCP 安全命令中心）集成到 Azure 安全中心。

此功能意味着安全中心可以在所有主要的云环境中提供可见性和保护。 此集成的一些优势如下：

- 自动代理预配 - 安全中心使用 Azure Arc 将 Log Analytics 代理部署到 AWS 实例
- 策略管理
- 漏洞管理
- 嵌入的终结点检测和响应 (EDR)
- 检测安全配置错误
- 显示所有云提供商提供的安全建议的单一视图
- 将所有资源整合到安全中心的安全分数计算中
- AWS 和 GCP 资源的法规符合性评估

在安全中心的菜单中，选择“多云连接器”，随即将显示用于新建连接器的选项：

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="安全中心“多云连接器”页面上的“添加 AWS 帐户”按钮":::

了解详细信息，请参阅：
- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>从订阅和管理组的安全分数中免除所有建议

我们正在扩展免除功能，以免除所有建议。 提供其他选项来微调安全中心针对订阅、管理组或资源提出的安全建议。

有时，当你知道问题已被第三方工具解决，而安全中心未检测到时，资源将被列为运行不正常。 或者，建议会在你认为它不属于的范围内显示。 该建议可能不适用于特定的订阅。 或者，组织可能决定接受与特定资源或建议相关的风险。

使用此预览功能，现在可以针对建议创建免除，以执行以下操作：

- 免除某资源，以确保其将来不会被列入运行不正常的资源，并且不会影响安全分数。 该资源将被列为不适用，原因将显示“已免除”以及你选择的特定理由。

- 免除某订阅或管理组，以确保建议不会影响安全分数，并且将来不会针对该订阅或管理组显示。 这与现有资源以及将来创建的任何资源相关。 建议将标记有你针对所选范围选择的特定理由。

了解详细信息，请参阅[从安全评分中免除资源和建议](exempt-resource.md)。



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>用户现在可以向其全局管理员请求租户范围内的可见性

如果用户不具有查看安全中心数据的权限，他们现在将会看到一个链接，用于向其组织的全局管理员请求权限。 该请求包括他们想要的角色以及需要这样做的理由。

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="横幅通知用户他们可以请求租户范围内的权限。":::

了解详细信息，请参阅[当你的权限不足时请求租户范围内的权限](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围

[Azure 安全基准](/security/benchmark/azure/introduction)是 Azure 安全中心的默认策略计划。 

为扩大此基准的覆盖范围，安全中心已添加下列 35 条预览建议。

> [!TIP]
> 预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。

| 安全控制                     | 新建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 启用静态加密            | - Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据<br>- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密<br>- 应为 MySQL 服务器启用“创建自己的密钥”数据保护<br>- 应为 PostgreSQL 服务器启用“创建自己的密钥”数据保护<br>- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密<br>- 容器注册表应使用客户管理的密钥 (CMK) 进行加密<br>- SQL 托管实例应使用客户管理的密钥进行静态数据加密<br>- SQL Server 应使用客户管理的密钥进行静态数据加密<br>- 存储帐户应使用客户管理的密钥 (CMK) 进行加密                                                                                                                                                              |
| 实现安全最佳实践    | - 订阅应有一个联系人电子邮件地址，用于接收安全问题通知<br> - 你的订阅应启用 Log Analytics 代理自动预配<br> - 应启用高严重性警报的电子邮件通知<br> - 应启用向订阅所有者发送高严重性警报的电子邮件通知<br> - 密钥保管库应启用清除保护<br> - 密钥保管库应启用软删除 |
| 管理访问和权限        | - 确保函数应用已启用“客户端证书(传入客户端证书)” |
| 保护应用程序免受 DDoS 攻击 | - 应为应用程序网关启用 Web 应用程序防火墙 (WAF)<br> - 应为 Web 应用程序防火墙 (WAF) 启用 Azure Front Door 服务 |
| 限制未经授权的网络访问 | - 应在 Key Vault 上启用防火墙<br> - 应为 Key Vault 配置专用终结点<br> - 应用程序配置应使用专用链接<br> - Azure Cache for Redis 应驻留在虚拟网络中<br> - Azure 事件网格域应使用专用链接<br> - Azure 事件网格主题应使用专用链接<br> - Azure 机器学习工作区应使用专用链接<br> - Azure SignalR 服务应使用专用链接<br> - Azure Spring Cloud 应使用网络注入<br> - 容器注册表不得允许无限制的网络访问<br> - 容器注册表应使用专用链接<br> - 应为 MariaDB 服务器禁用公用网络访问<br> - 应为 MySQL 服务器禁用公用网络访问<br> - 应为 PostgreSQL 服务器禁用公用网络访问<br> - 存储帐户应使用专用链接连接<br> - 存储帐户应使用虚拟网络规则来限制网络访问<br> - VM 映像生成器模板应使用专用链接|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相关链接：

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>将经筛选的建议列表导出为 CSV 

2020 年 11 月，我们在“建议”页面中添加了筛选器（[建议列表现包含筛选器](release-notes-archive.md#recommendations-list-now-includes-filters)）。 12 月，我们扩展了这些筛选器（[建议页面包含用于环境、严重性和可用响应的新筛选器](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)）。 

随着此次公告的发布，我们将更改为“下载到 CSV”按钮的行为，使 CSV 导出仅包含经筛选的列表中当前显示的建议。 

例如，在下图中，可以看到列表已筛选为显示两个建议。 生成的 CSV 文件包括受这两个建议影响的每项资源的状态详细信息。   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="将经筛选的建议导出到 CSV 文件。":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>在 Azure Policy 评估中，“不适用”资源现报告为“合规”

以前，为建议评估且发现不适用的资源在 Azure Policy 中显示为“不合规”。 任何用户操作都不能将资源状态更改为“合规”。 进行此更改后，为了显得更加清楚，将资源报告为“合规”。

唯一的影响是 Azure Policy 中合规资源的数量将增加。 Azure 安全中心的安全评分不受影响。


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>通过连续导出（预览版）导出安全分数和法规合规性数据的每周快照

我们已在[连续导出](continuous-export.md)工具中添加了新的预览功能，用于导出安全分数和法规合规性数据的每周快照。

定义连续导出时，请设置导出频率：

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="选择连续导出的频率。":::

- **流式处理** - 更新资源的运行状况时，将发送评估（如果没有更新，则不发送任何数据）。
- **快照** - 每周将发送所有法规合规性评估的当前状态的快照（这是面向安全分数和法规合规性数据每周快照的一项预览功能）。

请在[持续导出安全中心数据](continuous-export.md)中详细了解此功能的所有性能。