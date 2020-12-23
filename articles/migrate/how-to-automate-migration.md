---
title: 在 Azure Migrate 中自动执行无代理 VMware 迁移
description: 介绍如何使用脚本在 Azure Migrate 中迁移大量 VMware Vm
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754260"
---
# <a name="scale-migration-of-vmware-vms"></a>VMware Vm 的规模迁移 

本文将帮助你了解如何使用脚本通过无代理方法将大量 VMware 虚拟机迁移 (Vm) 。 若要缩放迁移，请使用 [Azure Migrate PowerShell 模块](./tutorial-migrate-vmware-powershell.md)。 

可在 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) 存储库中下载 Azure Migrate VMware 迁移自动化脚本。 可以使用脚本将 VMware Vm 迁移到 Azure，方法是使用无代理迁移方法。 [此处](./tutorial-migrate-vmware-powershell.md)介绍了这些脚本中使用的 Azure Migrate PowerShell 命令。

## <a name="current-limitations"></a>当前限制
- 这些脚本支持将 VMware Vm 迁移到所有磁盘。 如果要有选择地复制附加到 VMware VM 的磁盘，可以更新脚本。 
- 脚本支持使用评估建议。 如果未使用评估建议，则附加到 VMware VM 的所有磁盘都将迁移到相同的托管磁盘类型 (标准 "或" 高级 ") 。 如果希望将多种类型的托管磁盘用于同一 VM，则可以更新脚本

## <a name="prerequisites"></a>必备知识

- [完成发现教程](tutorial-discover-vmware.md)来准备 Azure 和 VMware，以便进行迁移。
- 建议先完成第二篇教程以[评估 VMware VM](./tutorial-assess-vmware-azure-vm.md)，然后再将它们迁移到 Azure。
- 设置 Azure PowerShell `Az` 模块。 如需安装或升级 Azure PowerShell，请遵循此 [Azure PowerShell 安装和配置指南](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>安装 Azure Migrate PowerShell 模块

Azure Migrate PowerShell 模块以预览版提供。 需要使用以下命令安装 PowerShell 模块。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV 输入文件
完成所有先决条件后，需要创建一个 CSV 文件，其中包含要迁移的每个源 VM 的数据。 所有脚本都设计为在同一 CSV 文件上工作。 脚本文件夹中提供了一个示例 CSV 模板以供参考。 Csv 文件是可配置的，因此你可以使用评估建议，甚至可以指定是否不会为特定 VM 触发特定操作。 

> [!NOTE]
> 同一 csv 文件可用于迁移多个 Azure Migrate 项目中的 Vm。

### <a name="csv-file-schema"></a>CSV 文件架构

**列标题** | **说明**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | 提供 Azure Migrate 项目订阅 ID。
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | 提供 Azure Migrate 资源组名称。
AZMIGRATEPROJECT_NAME | 提供要迁移服务器的中 Azure Migrate 项目的名称。 
SOURCE_MACHINE_NAME | 提供 Azure Migrate 项目中发现的 VM (显示名称) 的友好名称。
AZMIGRATEASSESSMENT_NAME | 提供需要用于迁移的评估的名称。
AZMIGRATEGROUP_NAME | 提供用于 Azure Migrate 评估的组的名称。
TARGET_RESOURCE_GROUP_NAME | 提供要迁移到的 Azure 资源组的名称。
TARGET_VNET_NAME| 提供已迁移的 VM 应使用的 Azure 虚拟网络的名称。
TARGET_SUBNET_NAME | 提供迁移的 VM 应使用的目标虚拟网络中的子网名称。 如果留空，则将使用 "默认" 子网。
TARGET_MACHINE_NAME | 提供已迁移的 VM 应在 Azure 中使用的名称。 如果留空，则将使用源计算机名称。  
TARGET_MACHINE_SIZE | 提供 VM 应在 Azure 中使用的 SKU。 若要将 VM 迁移到 Azure 中的 D2_v2 VM，请将此字段中的值指定为 "Standard_D2_v2"。 如果使用评估，则将根据评估建议得出此值。
LICENSE_TYPE | 指定是否要将 Azure 混合权益用于 Windows Server Vm。 使用值 "WindowsServer" 利用 Azure 混合权益。 否则留空或使用 "NoLicenseType"。
OS_DISK_ID | 提供要迁移的 VM 的 OS 磁盘 ID。 要使用的磁盘 ID 是使用 Get-AzMigrateServer cmdlet 检索到的磁盘 (UUID) 属性的唯一标识符。 如果未提供任何值，则脚本将使用 VM 的第一个磁盘作为 OS 磁盘。
TARGET_DISKTYPE | 提供要用于 Azure 中 VM 的所有磁盘的磁盘类型。 为高级托管磁盘使用 "Premium_LRS"，将 "StandardSSD_LRS" 用于标准 SSD 磁盘，使用 "Standard_LRS" 以使用标准 HDD 磁盘。 如果选择使用评估，则脚本将使用 VM 的每个磁盘的推荐磁盘类型来确定优先级。 如果不使用评估或指定任何值，则默认情况下，该脚本将使用标准 HDD 磁盘。    
AVAILABILITYZONE_NUMBER | 指定要用于已迁移 VM 的可用性区域编号。 如果不想使用可用性区域，可以保留此项为空。 
AVAILABILITYSET_NAME | 指定要用于已迁移 VM 的可用性集的名称。 如果不想使用可用性集，可以保留此项为空。
TURNOFF_SOURCESERVER | 如果要在迁移时关闭源 VM，请指定 "Y"。 否则，请使用 "N"。 如果留空，则脚本将值视为 "N"。
TESTMIGRATE_VNET_NAME | 指定要用于测试迁移的虚拟网络的名称。
UPDATED_TARGET_RESOURCE_GROUP_NAME | 如果要更新要由 Azure 中的已迁移 VM 使用的资源组，请指定 Azure 资源组的名称，否则保留为空。 
UPDATED_TARGET_VNET_NAME | 如果要更新要由 Azure 中的已迁移 VM 使用的虚拟网络，请指定 Azure 虚拟网络的名称，否则保留为空。
UPDATED_TARGET_MACHINE_NAME | 如果要更新 Azure 中已迁移 VM 使用的名称，请指定要使用的新名称，否则保留为空。
UPDATED_TARGET_MACHINE_SIZE | 如果要更新由 Azure 中的已迁移 VM 使用的 SKU，请指定要使用的新 SKU，否则保留为空。
UPDATED_AVAILABILITYZONE_NUMBER | 如果要更新要由 Azure 中的已迁移 VM 使用的可用性区域，请指定要使用的新可用性区域，否则保留为空。
UPDATED_AVAILABILITYSET_NAME | 如果要将可用性集更新为在 Azure 中迁移的 VM 所使用的可用性集，请指定要使用的新可用性集，否则保留为空。
UPDATE_NIC1_ID | 指定要更新的 NIC 的 ID。 如果留空，则脚本假定值为发现的 VM 的第一个 NIC。 如果不想更新 VM 的 NIC，请将包含 NIC 名称的所有字段保留为空。 
UPDATED_TARGET_NIC1_SELECTIONTYPE | 指定要用于此 NIC 的值。 使用 "主要"、"辅助" 或 "DoNotCreate" 来指定此 NIC 应该是主 NIC、辅助 NIC 还是不应该在迁移的 VM 上创建。 只能将一个 NIC 指定为 VM 的主要的 NIC。 如果不想更新，则保留为空。
UPDATED_TARGET_NIC1_SUBNET_NAME | 指定要用于已迁移 VM 上的 NIC 的子网名称。 如果不想更新，则保留为空。
UPDATED_TARGET_NIC1_IP | 如果要使用静态 IP，请指定要由已迁移 VM 上的 NIC 使用的 IPv4 地址。 如果要自动分配 IP，请使用 "自动"。 如果不想更新，则保留为空。
UPDATE_NIC2_ID | 指定要更新的 NIC 的 ID。 如果留空，则脚本假定值为发现的 VM 的第二个 NIC。 如果不想更新 VM 的 NIC，请将包含 NIC 名称的所有字段保留为空。
UPDATED_TARGET_NIC2_SELECTIONTYPE | 指定要用于此 NIC 的值。 使用 "主要"、"辅助" 或 "DoNotCreate" 来指定此 NIC 应该是主 NIC、辅助 NIC 还是不应该在迁移的 VM 上创建。 只能将一个 NIC 指定为 VM 的主要的 NIC。 如果不想更新，则保留为空。
UPDATED_TARGET_NIC2_SUBNET_NAME | 指定要用于已迁移 VM 上的 NIC 的子网名称。 如果不想更新，则保留为空。
UPDATED_TARGET_NIC2_IP | 如果要使用静态 IP，请指定要由已迁移 VM 上的 NIC 使用的 IPv4 地址。 如果要自动分配 IP，请使用 "自动"。 如果不想更新，则保留为空。
OK_TO_UPDATE | 使用 "Y" 指示运行 AzMigrate_UpdateMachineProperties 脚本时是否需要更新 VM 属性。 使用 "N" 或保留为空。
OK_TO_MIGRATE | 使用 "Y" 指示在运行 AzMigrate_StartMigration 脚本时是否应迁移 VM。 如果你不想迁移 VM，请使用 "N" 或保留为空。 
OK_TO_USE_ASSESSMENT | 在运行 AzMigrate_StartReplication 脚本时，请使用 "Y" 指示 VM 是否应使用评估建议来启动复制。 这将覆盖 csv 文件中的 TARGET_MACHINE_SIZE 和 TARGET_DISKTYPE 值。 如果你不想使用评估建议，请使用 "N" 或留空。
OK_TO_TESTMIGRATE | 使用 "Y" 指示运行 AzMigrate_StartTestMigration 脚本时是否应迁移 VM。 如果你不想测试迁移 VM，请使用 "N" 或留空。 
OK_TO_RETRIEVE_REPLICATIONSTATUS | 使用 "Y" 指示在运行 AzMigrate_ReplicationStatus 脚本时是否应更新 VM 的复制状态。 如果你不想更新复制状态，请使用 "N" 或保留为空。
OK_TO_CLEANUP | 使用 "Y" 指示在运行 AzMigrate_StopReplication 脚本时是否应清除 VM 的复制。 使用 "N" 或保留为空。
OK_TO_TESTMIGRATE_CLEANUP | 使用 "Y" 指示在运行 AzMigrate_CleanUpTestMigration 脚本时是否应清除 VM 的测试迁移。 使用 "N" 或保留为空。


## <a name="script-execution"></a>脚本执行

CSV 准备就绪后，可以执行以下步骤来迁移本地 VMware Vm。

**步骤编号** | **脚本名称** | **说明**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | 为 csv 中列出的所有 Vm 启用复制，该脚本将创建 CSV 输出和日志文件以进行故障排除。
2 | AzMigrate_ReplicationStatus.ps1 | 检查复制的状态，该脚本将创建一个 csv 输出，其中包含每个 VM 的状态和一个用于疑难解答的日志文件。
3 | AzMigrate_UpdateMachineProperties.ps1 | Vm 完成初始复制后，使用此脚本更新 VM 的目标属性 (计算和网络属性) 。 此脚本将创建包含每个 VM 的作业详细信息的 CSV 输出。
4 | AzMigrate_StartTestMigration.ps1 |  对于为测试迁移而配置的 csv 中列出的所有 Vm，启动测试故障转移。 此脚本将创建包含每个 VM 的作业详细信息的 CSV 输出。
5 | AzMigrate_CleanUpTestMigration.ps1 | 手动验证正在进行测试故障转移的 Vm 后，请使用此脚本为 csv 中列出的所有已为测试迁移清理配置的 Vm 清理测试故障转移 Vm。 此脚本将创建包含每个 VM 的作业详细信息的 CSV 输出。
6 | AzMigrate_StartMigration.ps1 | 为 csv 中列出的用于迁移的所有 Vm 启动迁移。 此脚本将创建包含每个 VM 的作业详细信息的 CSV 输出。
7 | AzMigrate_StopReplication.ps1 | 成功迁移 VM 后，停止复制 VM，或者出于其他原因要取消复制。 此脚本将创建包含每个 VM 的作业详细信息的 CSV 输出。


以下脚本由其他脚本为所有 Azure Migrate 操作调用，如启用复制、启动测试迁移、更新 VM 属性等。 确保所有脚本都出现在同一文件夹/路径中。 

**步骤编号** | **脚本名称** | **说明**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | 常见的脚本，其中包含用于通过 API) 、发现的 Vm 和复制 Vm (检索评估属性的函数。 
2 | AzMigrate_CSV_Processor.ps1 | 常见脚本，其中包含用于 csv 文件操作的函数，包括日志的加载、读取和打印功能。 
3 | AzMigrate_Logger.ps1 | 为生成 Azure Migrate 自动化操作的日志文件而调用的公共脚本。 日志文件的格式将为 log.Scriptname.Datetime.txt。

除了上述各项，该文件夹还包含 AzMigrate_Template.ps1，其中包含用于构建不同 Azure Migrate 操作的自定义脚本的框架框架。 

### <a name="script-execution-syntax"></a>脚本执行语法

下载脚本后，可以按如下所示执行脚本。

如果要使用 Input.csv 文件执行脚本以启动 Vm 复制，请使用以下语法。 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

若要详细了解如何使用 Azure PowerShell 通过 Azure Migrate 迁移 VMware Vm，请遵循 [教程](./tutorial-migrate-vmware-powershell.md)。