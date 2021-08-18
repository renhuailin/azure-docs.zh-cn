---
title: 社区工具 - 将经典资源移到 Azure 资源管理器
description: 本文编录了社区提供的工具，这些工具可帮助将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: b7d966967988a17065be16ab37c123f17c8d86b3
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112323351"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>用于将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型的社区工具

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](classic-vm-deprecation.md#how-does-this-affect-me)。

本文编录了社区提供的工具，这些工具可帮助将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。

> [!NOTE]
> Microsoft 支持服务并不正式支持这些工具。 因此 GitHub 上提供了其开放源代码，我们很乐意接受有关修复或其他方案的 PR。 若要报告问题，请使用 GitHub 问题功能。
> 
> 使用这些工具进行迁移会造成经典虚拟机停机。 若要了解平台支持的迁移，请访问 
> 
>   * [平台支持从经典部署模型迁移到 Azure Resource Manager 堆栈的 IaaS 资源](migration-classic-resource-manager-overview.md)
>   * [有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
>   * [使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
这是一个帮助程序工具集合，它是在从 Azure 服务管理到 Azure Resource Manager 的企业迁移过程中创建的。 借助此工具，可将基础结构复制到另一个订阅，可将其用于在生产订阅中运行迁移前测试迁移并消除所有问题。

[工具文档链接](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz 是另一选项，用于将整套经典 IaaS 资源迁移到 Azure Resource Manager IaaS 资源。 可以在同一订阅中迁移，也可以在不同订阅和不同订阅类型（例如：CSP 订阅）之间迁移。

[工具文档链接](https://github.com/Azure/migAz)

## <a name="next-steps"></a>后续步骤

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-cli.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.yml)
