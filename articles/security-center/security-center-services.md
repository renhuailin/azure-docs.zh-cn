---
title: 关于 OS、计算机类型和云的 Azure 安全中心的功能
description: 根据 OS、计算机类型和云部署了解 Azure 安全中心功能的可用性。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 3174aff7529e8248cffb39ee10684e843b4fd0b1
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418568"
---
# <a name="feature-coverage-for-machines"></a>适用于计算机的功能覆盖范围

下面的两个选项卡显示了可用于 Windows 和 Linux 计算机的 Azure 安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虚拟机和服务器支持的功能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 计算机**](#tab/features-windows)

|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**已启用 Azure Arc 的计算机**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 集成](security-center-wdatp.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|是|
|[虚拟机行为分析（和安全警报）](alerts-reference.md)|✔|✔|✔|是|
|[无文件安全警报](alerts-reference.md#alerts-windows)|✔|✔|✔|是|
|[基于网络的安全警报](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[恰时 VM 访问](security-center-just-in-time.md)|✔|-|-|是|
|[本机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|是|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[合规性仪表板和报表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|[Docker 主机强化](./harden-docker-hosts.md)|-|-|-|是|
|缺少 OS 修补程序评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|安全配置错误评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|[终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|磁盘加密评估|✔</br>（适用于[支持的场景](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)）|✔|-|否|
|第三方漏洞评估|✔|-|✔|否|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|否|
||||||

### <a name="linux-machines"></a>[**Linux 计算机**](#tab/features-linux)

| **功能**                                                                                                               | **Azure 虚拟机**                                                                                      | **Azure 虚拟机规模集** | **已启用 Azure Arc 的计算机** | **需要 Azure Defender**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Microsoft Defender for Endpoint 集成](security-center-wdatp.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | 是                              |
| [虚拟机行为分析（和安全警报）](./azure-defender.md)                                         | ✔</br>（在支持的版本上）                                                                                  | ✔</br>（在支持的版本上）        | ✔                             | 是                               |
| [无文件安全警报](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | 是                               |
| [基于网络的安全警报](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | 是                               |
| [恰时 VM 访问](security-center-just-in-time.md)                                                                 | ✔                                                                                                              | -                                    | -                              | 是                               |
| [本机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | 是                               |
| [文件完整性监视](security-center-file-integrity-monitoring.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | 是                               |
| [自适应应用程序控制](security-center-adaptive-application.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | 是                               |
| [网络映射](security-center-network-recommendations.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | 是                               |
| [自适应网络强化](security-center-adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | 是                               |
| [合规性仪表板和报表](security-center-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | 是                               |
| [Docker 主机强化](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | 是                               |
| 缺少 OS 修补程序评估                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure：否<br><br>已启用 Arc：是 |
| 安全配置错误评估                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure：否<br><br>已启用 Arc：是 |
| [终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | 否                                |
| 磁盘加密评估                                                                                                | ✔</br>（适用于[支持的场景](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)） | ✔                                    | -                              | 否                                |
| 第三方漏洞评估                                                                                      | ✔                                                                                                              | -                                    | ✔                             | 否                                |
| [网络安全评估](security-center-network-recommendations.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | 否                                |
||||||

--- 


> [!TIP]
>要试验仅适用于 Azure Defender 的功能，可以注册 30 天试用版。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支持的终结点保护解决方案 <a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果你发现此列表中有终结点保护解决方案，安全中心会建议你不要安装该解决方案。

若要了解何时会针对其中的每种保护生成建议，请参阅[终结点保护评估和建议](security-center-endpoint-protection.md)。

| 解决方案                                                  | 受支持的平台                                    | 安全中心安装 | 安全中心发现 |
|-----------------------------------------------------------|--------------------------------------------------------|------------------------------|---------------------------|
| Microsoft Defender 防病毒                              | Windows Server 2016 或更高版本                           | 否（内置到 OS）           | 是                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2                                 | 通过扩展                | 是                       |
| Trend Micro – Deep Security                               | Windows Server（所有）                                   | 否                           | 是                       |
| Symantec v12.1.1100+                                      | Windows Server（所有）                                   | 否                           | 是                       |
| McAfee v10+                                               | Windows Server（所有）                                   | 否                           | 是                       |
| McAfee v10+                                               | Linux（预览版）                                        | 否                           | 是                       |
| Sophos V9+                                                | Linux（预览版）                                        | 否                           | 是                       |
|                                                           |                                                        |                              |                           |



## <a name="feature-support-in-government-and-sovereign-clouds"></a>政府云和主权云中的功能支持

| 功能/服务                                                                                                                                           | Azure          | Azure Government               | Azure 中国世纪互联   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **安全中心免费功能**                                                                                                                         |                |                                |               |
| - [连续导出](./continuous-export.md)                                                                                                             | GA             | GA                             | GA            |
| - [工作流自动化](./continuous-export.md)                                                                                                           | GA             | GA                             | GA            |
| - [建议例外规则](./exempt-resource.md)                                                                                                  | 公共预览版 | 不可用                  | 不可用 |
| - [警报抑制规则](./alerts-suppression-rules.md)                                                                                                | GA             | GA                             | GA            |
| - [安全警报的电子邮件通知](./security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | GA            |
| - [代理和扩展的自动预配](./security-center-enable-data-collection.md)                                                              | GA             | GA                             | GA            |
| - [资产清单](./asset-inventory.md)                                                                                                                 | GA             | GA                             | GA            |
| - [Azure 安全中心工作簿库中的 Azure Monitor 工作簿报表](./custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | GA            |
| **Azure Defender 计划和扩展**                                                                                                                   |                |                                |               |
| - [适用于服务器的 Azure Defender](./defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | GA            |
| - [适用于应用服务的 Azure Defender](./defender-for-app-service-introduction.md)                                                                            | GA             | 不可用                  | 不可用 |
| - [适用于 DNS 的 Azure Defender](./defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | GA            |
| - [适用于容器注册表的 Azure Defender](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [适用于容器注册表的 Azure Defender 扫描 CI/CD 工作流中的容器映像](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | 公共预览版 | 不可用                  | 不可用 |
| - [Azure Defender for Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [适用于启用了 Azure Arc 的 Kubernetes 群集的 Azure Defender 扩展](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | 公共预览版 | 不可用                  | 不可用 |
| - [适用于 Azure SQL 数据库服务器的 Azure Defender](./defender-for-sql-introduction.md)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [计算机上的 Azure Defender for SQL 服务器](./defender-for-sql-introduction.md)                                                                        | GA             | GA                             | 不可用 |
| - [适用于开源关系数据库的 Azure Defender](./defender-for-databases-introduction.md)                                                         | GA             | 不可用                  | 不可用 |
| - [适用于 Key Vault 的 Azure Defender](./defender-for-key-vault-introduction.md)                                                                                | GA             | 不可用                  | 不可用 |
| - [适用于资源管理器的 Azure Defender](./defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             | GA            |
| - [适用于存储的 Azure Defender](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | 不可用 |
| - [针对 Cosmos DB 的威胁防护](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | 公共预览版 | 不可用                  | 不可用 |
| - [Kubernetes 工作负载保护](./kubernetes-workload-protections.md)                                                                                  | GA             | GA                             | GA            |
| - [使用 Sentinel 进行双向警报同步](../sentinel/connect-azure-security-center.md)                                                      | 公共预览版 | 不可用                  | 不可用 |
| **适用于服务器功能的 Azure Defender** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [实时 VM 访问](./security-center-just-in-time.md)                                                                                             | GA             | GA                             | GA            |
| - [文件完整性监视](./security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             | GA            |
| - [自适应应用程序控制](./security-center-adaptive-application.md)                                                                              | GA             | GA                             | GA            |
| - [自适应网络强化](./security-center-adaptive-network-hardening.md)                                                                           | GA             | 不可用                  | 不可用 |
| - [Docker 主机强化](./harden-docker-hosts.md)                                                                                                       | GA             | GA                             | GA            |
| - [计算机的集成漏洞评估](./deploy-vulnerability-assessment-vm.md)                                                             | GA             | 不可用                  | 不可用 |
| - [合规性仪表板和报表](./security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Microsoft Defender for Endpoint 部署和集成许可证](./security-center-wdatp.md)                                                         | GA             | GA                             | 不可用 |
| - [连接 AWS 帐户](./quickstart-onboard-aws.md)                                                                                                      | GA             | 不可用                  | 不可用 |
| - [连接 GCP 帐户](./quickstart-onboard-gcp.md)                                                                                                      | GA             | 不可用                  | 不可用 |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1" /></a>1</sup> 部分 GA：禁用漏洞扫描的特定发现的功能处于公共预览状态。

<sup><a name="footnote2" /></a>2</sup> Azure 政府云上容器注册表的漏洞扫描只能通过“推送扫描”功能来执行。

<sup><a name="footnote3" /></a>3</sup> 需要适用于容器注册表的 Azure Defender。

<sup><a name="footnote4" /></a>4</sup> 部分 GA：对启用了 Arc 的群集的支持处于公开预览状态，在 Azure 政府中不可用。

<sup><a name="footnote5" /></a>5</sup> 需要 Azure Defender for Kubernetes。

<sup><a name="footnote6" /></a>6</sup> 部分 GA：适用于存储的 Azure Defender 中的部分威胁防护警报处于公共预览状态。

<sup><a name="footnote7" /></a>7</sup> 这些功能都需要[适用于服务器的 Azure Defender](./defender-for-servers-introduction.md)。

<sup><a name="footnote8" /></a>8</sup> 每个云类型提供的标准可能存在差异。
 
<sup><a name="footnote9" /></a>9</sup> 部分 GA：SQL Server 警报和漏洞评估的子集。 行为威胁防护功能不可用。

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何使用 Log Analytics 代理收集数据](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 参阅[支持安全中心的平台](security-center-os-coverage.md)。