---
title: 关于 OS、计算机类型和云的 Azure 安全中心的功能
description: 了解根据其 OS、类型和云部署可以使用哪些 Azure 安全中心功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: f3d1918eafe8d61d9c5a7db3f29b83b5412914c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179553"
---
# <a name="feature-coverage-for-machines"></a>适用于计算机的功能覆盖范围

下面的两个选项卡显示了可用于 Windows 和 Linux 虚拟机和服务器的 Azure 安全中心功能。

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
|针对 Docker 托管的 IaaS 容器的建议和威胁防护|-|-|-|是|
|缺少 OS 修补程序评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|安全配置错误评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|[终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|磁盘加密评估|✔</br>（适用于[支持的场景](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)）|✔|-|否|
|第三方漏洞评估|✔|-|✔|否|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|否|


### <a name="linux-machines"></a>[**Linux 计算机**](#tab/features-linux)

|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**已启用 Azure Arc 的计算机**|**需要 Azure Defender**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 集成](security-center-wdatp.md)|-|-|-|是|
|[虚拟机行为分析（和安全警报）](./azure-defender.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|是|
|[无文件安全警报](alerts-reference.md#alerts-windows)|-|-|-|是|
|[基于网络的安全警报](other-threat-protections.md#network-layer)|✔|✔|-|是|
|[恰时 VM 访问](security-center-just-in-time.md)|✔|-|-|是|
|[本机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|是|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|是|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|是|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|是|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|是|
|[合规性仪表板和报表](security-center-compliance-dashboard.md)|✔|✔|✔|是|
|针对 Docker 托管的 IaaS 容器的建议和威胁防护|✔|✔|✔|是|
|缺少 OS 修补程序评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|安全配置错误评估|✔|✔|✔|Azure：否<br><br>已启用 Arc：是|
|[终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|否|
|磁盘加密评估|✔</br>（适用于[支持的场景](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)）|✔|-|否|
|第三方漏洞评估|✔|-|✔|否|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|否|

--- 


> [!TIP]
>要试验仅适用于 Azure Defender 的功能，可以注册 30 天试用版。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支持的终结点保护解决方案 <a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果你发现此列表中有终结点保护解决方案，安全中心会建议你不要安装该解决方案。

若要了解何时会针对其中的每种保护生成建议，请参阅[终结点保护评估和建议](security-center-endpoint-protection.md)。

| 终结点保护| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Microsoft Defender 防病毒| Windows Server 2016 或更高版本| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| Trend Micro – Deep Security | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 服务器系列  | 否 | 是 * *\** _ |
| Sophos V9+| Linux 服务器系列  | 否 | 是 _*\**_  |

 _*\**_ 覆盖率状态和支持数据目前仅在与受保护订阅关联的 Log Analytics 工作区中可用。 这些数据不会在 Azure 安全中心门户中反映。

> [!NOTE]
> 在 Windows Server 2008 R2 虚拟机上检测 System Center Endpoint Protection (SCEP) 需要在 PowerShell（v3.0 或更高版本）之后安装 SCEP。



## <a name="feature-support-in-government-clouds"></a>政府云中的功能支持

| 服务/功能 | US Gov | China Gov |
|------|:----:|:----:|
|[实时 VM 访问](security-center-just-in-time.md) (1)|✔|✔|
|[文件完整性监视](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[自适应应用程序控制](security-center-adaptive-application.md) (1)|✔|✔|
|[自适应网络强化](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker 主机强化](harden-docker-hosts.md) (1)|✔|✔|
|[的集成漏洞评估](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[用于终结点的 Microsoft Defender](harden-docker-hosts.md) (1)|✔|-|
|[连接 AWS 帐户](quickstart-onboard-aws.md) (1)|-|-|
|[连接 GCP 帐户](quickstart-onboard-gcp.md) (1)|-|-|
|[连续导出](continuous-export.md)|✔|✔ (2)|
|[工作流自动化](workflow-automation.md)|✔|✔|
|[建议例外规则](exempt-resource.md)|-|-|
|[警报抑制规则](alerts-suppression-rules.md)|✔|✔|
|[安全警报的电子邮件通知](security-center-provide-security-contact-details.md)|✔|✔|
|[资产清单](asset-inventory.md)|✔|✔|
|[适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)|-|-|
|[适用于存储的 Azure Defender](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender for SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)|-|-|
|[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md)|-|-|
|[适用于 DNS 的 Azure Defender](defender-for-dns-introduction.md)|-|-|
|[适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)|✔|✔|
|[适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|||

(1) 需要用于服务器的 Azure Defender*

(2) 部分完成


## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何使用 Log Analytics 代理收集数据](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 查看[支持安全中心的平台](security-center-os-coverage.md)。