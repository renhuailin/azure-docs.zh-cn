---
title: Azure Defender 和可用计划概述
description: 了解 Azure Defender 的计划、保护和警报。 然后在订阅上针对高级安全启用 Azure Defender 。
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cfbb8badd4a0d9a8b9776d810d33f77a051b3a39
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238957"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 简介

Azure 安全中心的功能涵盖了云安全性的两大重要领域：

- **云安全态势管理 (CSPM)** - 安全中心对所有 Azure 用户均免费。 免费体验包括 CSPM 功能，如安全功能分数、Azure 计算机中的安全错误配置检测、资产清单等。 使用这些 CSPM 功能增强混合云态势，并跟踪内置策略的合规性。

- **云工作负载保护 (CWP)** - 集成到安全中心内部的云工作负载保护平台 (CWPP)，用于为 Azure 和混合资源及工作负载提供高级智能的保护。 启用 Azure Defender 可带来一系列其他安全功能，如本页所述。 启用任何 Azure Defender 计划后，除了内置策略，还可以添加自定义策略和计划。 可以添加法规标准（例如 NIST 和 Azure CIS）以及 [Azure 安全基准](/security/benchmark/azure/introduction)，以获得真正的合规性自定义视图。

可在你的环境中使用安全中心的 Azure Defender 仪表板显示和控制 CWP 功能：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 仪表板示例。" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可以保护哪些资源类型？

Azure Defender 为虚拟机、SQL 数据库、容器、web 应用程序、网络等提供安全警报和高级威胁防护。

从 Azure 安全中心的“定价和设置”区域启用 Azure Defender 时，将同时启用以下 Defender 计划，并为环境的计算、数据和服务层提供全面防护：

- [适用于服务器的 Azure Defender](defender-for-servers-introduction.md)
- [适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)
- [适用于存储的 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)
- [适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)
- [适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md)
- [适用于 DNS 的 Azure Defender](defender-for-dns-introduction.md)
- [用于开源关系数据库的 Azure Defender](defender-for-databases-introduction.md)

安全中心的文档对其中每个计划单独进行了介绍。

> [!TIP]
> Azure Defender for IoT（预览版）是一种单独的产品。 可在 [Azure Defender for IoT（预览版）简介](../defender-for-iot/overview.md)中找到所有详细信息。 

## <a name="hybrid-cloud-protection"></a>混合云保护

与保护 Azure 环境一样，也可以在混合云环境中添加 Azure Defender 功能：

- 保护非 Azure 服务器
- 保护其他云中的虚拟机（例如 AWS 和 GCP）

可根据你的特定环境获取自定义威胁智能和设置了优先级别的警报，这样你便能够专注于最重要的事务。

若要将 Azure Defender 的保护扩展到其他云中或本地的虚拟机和 SQL 数据库，可部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 并启用 Azure Defender。 Azure Arc for servers 是一项免费服务，但在启用了 Arc 的服务器上使用的服务（例如 Azure Defender）将按照该服务的定价收费。 详细了解[使用 Azure Arc 添加非 Azure 计算机](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)。

> [!TIP]
> 适用于 AWS 的本机连接器以透明方式处理 Azure Arc 部署。 在[将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)中了解详细信息。



## <a name="azure-defender-security-alerts"></a>Azure Defender 安全警报 

当 Azure Defender 检测到环境中的任何区域遭到威胁时，会生成安全警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。

无论警报是由安全中心生成，还是由安全中心从集成的安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报流式传输到 SIEM、SOAR，或 IT 服务管理解决方案](export-to-siem.md)中的说明操作。

> [!NOTE]
> 来自不同源的警报可能在不同的时间后出现。 例如，需要分析网络流量的警报的出现时间，可能比虚拟机上运行的可疑进程的相关警报要晚一些。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 高级保护功能

Azure Defender 在定制与资源相关的建议时会使用高级分析。 

保护措施包括使用实时访问和自适应应用程序控件保护 VM 的管理端口，创建允许列表来确定在计算机上应或不应运行哪些应用。 

使用 Azure Defender 仪表板中的高级保护磁贴来监视和配置每种保护措施。 

## <a name="vulnerability-assessment-and-management"></a>漏洞评估和管理

Azure Defender 为你的虚拟机和容器注册表提供漏洞扫描，且无需额外付费。 扫描程序由 Qualys 提供支持，但你无需具备 Qualys 许可证，甚至也不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。 

查看这些漏洞扫描程序中的发现结果，并相应从安全中心内部作出全部响应。 这使安全中心更接近于用于集中了解所有云安全工作情况的统一视窗。

通过以下页面了解详细信息：

- [Azure 虚拟机安全中心的集成漏洞评估解决方案](deploy-vulnerability-assessment-vm.md)
- [标识 Azure 容器注册表映像中的漏洞](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Defender 的优点。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)