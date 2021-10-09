---
title: 部署 SSIS 包
description: 了解如何通过 Azure-SSIS 集成运行时在 Azure 数据工厂中部署和运行 SSIS 包。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e12968b993a68f8eb0da179c6b4de57256a0c63b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219254"
---
# <a name="deploy-ssis-packages"></a>部署 SSIS 包

配置 Azure-SSIS 集成运行时后，可以直接在 Azure 中部署和运行包。

## <a name="using-ssisdb"></a>使用 SSISDB

如果使用 SSISDB，可将包部署到其中，并使用已启用 Azure 的 SSDT 或 SSMS 工具在 Azure-SSIS IR 上运行它们。 这些工具通过数据库服务器的服务器终结点来与该服务器建立连接： 

- 对于 Azure SQL 数据库服务器，服务器终结点格式为 `<server name>.database.windows.net`。
- 对于具有专用终结点的托管实例，服务器终结点格式为 `<server name>.<dns prefix>.database.windows.net`。
- 对于具有公共终结点的托管实例，服务器终结点格式为 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

## <a name="using-file-system-azure-files-or-msdb"></a>使用文件系统、Azure 文件存储或 MSDB

如果不使用 SSISDB，则可以将包部署到由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 MSDB 中，并使用 [dtutil](/sql/integration-services/dtutil-utility) 和 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) 命令行实用工具在 Azure-SSIS IR 上运行它们。 

有关详细信息，请参阅[部署 SSIS 项目/包](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)。

在这两种情况下，还可以使用数据工厂管道中的“执行 SSIS 包”活动在 Azure-SSIS IR 上运行已部署的包。 有关详细信息，请参阅[以第一类数据工厂活动的形式调用 SSIS 包执行](./how-to-invoke-ssis-package-ssis-activity.md)。

## <a name="next-steps"></a>后续步骤

- [了解如何使用 Azure 门户预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-portal.md)。
- [了解如何使用 Azure PowerShell 预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-powershell.md)。
- [了解如何使用 Azure 资源管理器模板预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-resource-manager-template.md)。

请参阅本文档中的其他 Azure-SSIS IR 主题：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般信息。
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索并了解有关 Azure-SSIS IR 的信息。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加更多节点来横向扩展 Azure-SSIS IR。
- [在 Azure 中部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [在 Azure 中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
