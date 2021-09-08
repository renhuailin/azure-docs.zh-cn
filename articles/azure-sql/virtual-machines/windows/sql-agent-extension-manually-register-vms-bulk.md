---
title: 将 Azure 中的多个 SQL VM 注册到 SQL IaaS 代理扩展
description: 将 SQL Server VM 批量注册到 SQL IaaS 代理扩展，可提高可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b2066ef94cb87a9ab9c000615c018938cbeddb1
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220633"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>将 Azure 中的多个 SQL VM 注册到 SQL IaaS 代理扩展
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了如何使用 `Register-SqlVMs`Azure  PowerShell cmdle 将Azure 中的 SQL Server 虚拟机 (VM) 批量注册到 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 


本文介绍如何批量手动注册 SQL Server VM。 或者，可以自动注册[所有 SQL Server VM](sql-agent-extension-automatic-registration-all-vms.md)，也可以[手动注册单个 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。 

> [!NOTE]
> 从 2021 年 9 月开始，在完整模式下通过 SQL IaaS 扩展进行注册不再需要重启 SQL Server 服务。 

## <a name="overview"></a>概述

`Register-SqlVMs` cmdlet 可用于注册给定订阅、资源组或特定虚拟机列表中的所有虚拟机。 Cmdlet 将在 [lightweight_ management 模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)下注册虚拟机，然后生成[报告和日志文件](#output-description)。 

注册过程没有风险，没有停机时间，也不会重启 SQL Server 服务或虚拟机。 

## <a name="prerequisites"></a>先决条件

若要将 SQL Server VM 注册到代理扩展，需要以下内容： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)，该订阅已[注册 Microsoft.SqlVirtualMachine 资源提供程序](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp)，并包含未注册的 SQL Server 虚拟机。 
- 用于注册虚拟机的客户端凭据存在于以下任何 Azure 角色中：“虚拟机参与者”、“参与者”和“所有者”。 
- [Az PowerShell（最低 5.0）](/powershell/azure/new-azureps-module-az)的最新版本。 


## <a name="get-started"></a>入门

在继续操作之前，必须首先创建脚本的本地副本，将其导入为 PowerShell 模块，然后连接到 Azure。 

### <a name="create-the-script"></a>创建脚本

要创建脚本，请复制本文末尾处的[完整脚本](#full-script)并将其作为 `RegisterSqlVMs.psm1` 保存在本地。 

### <a name="import-the-script"></a>导入脚本

创建脚本后，可以将它作为模块导入 PowerShell 终端。 

打开管理 PowerShell 终端并导航到保存 `RegisterSqlVMs.psm1` 文件的位置。 然后，运行以下 PowerShell cmdlet，将脚本作为模块导入： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>连接到 Azure

运行以下 PowerShell cmdlet 以连接到 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>订阅列表中的所有 VM 

使用以下 cmdlet 注册订阅列表中的所有 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

示例输出： 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>单个订阅中的所有 VM

使用以下 cmdlet 在单个订阅中注册所有 SQL Server 虚拟机： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

示例输出：

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>多个资源组中的所有 VM

使用以下 cmdlet 在单个订阅中注册多个资源组中的所有 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

示例输出：

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>一个资源组中的所有 VM

使用以下 cmdlet 在单个资源组中注册所有 SQL Server 虚拟机： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

示例输出：

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>单个资源组中的特定 VM

使用以下 cmdlet 在单个资源组中注册特定的 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

示例输出：

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>特定 VM

使用以下 cmdlet 注册特定的 SQL Server 虚拟机： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

示例输出：

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>输出说明

每次使用 `Register-SqlVMs` cmdlet 时都会生成报表和日志文件。 

### <a name="report"></a>报表

生成的报表是一个名为 `RegisterSqlVMScriptReport<Timestamp>.txt` 的 `.txt` 文件，其中时间戳是启动 cmdlet 的时间。 报表列出了以下详细信息：

| **输出值** | **说明** |
| :--------------  | :-------------- | 
| 由于你没有访问权限或凭据不正确而导致的订阅注册失败的次数 | 这提供了与提供的身份验证有问题的订阅的数量和列表。 通过搜索订阅 ID，可以在日志中找到详细的错误。 | 
| 由于订阅未注册到资源提供程序而无法尝试的订阅数 | 本部分包含尚未注册到 SQL IaaS 代理扩展的订阅的计数和列表。 |
| 找到的 VM 总数 | 在传递给 cmdlet 的参数范围内找到的虚拟机的计数。 | 
| 已注册的 VM 数 | 由于虚拟机已注册到代理扩展而跳过的虚拟机的计数。 |
| 已成功注册的 VM 数 | 运行 cmdlet 后成功注册的虚拟机计数。 以 `SubscriptionID, Resource Group, Virtual Machine` 格式列出已注册的虚拟机。 | 
| 由于错误而未能注册的 VM 数 | 由于某些错误而未能注册的虚拟机计数。 可在日志文件中查看错误的详细信息。 | 
| 由于 VM 或 VM 上的来宾代理未运行而跳过的 VM 数 | 由于虚拟机或虚拟机上的来宾代理未运行而无法注册的虚拟机的计数和列表。 启动虚拟机或来宾代理后，可以重试这些程序。 可在日志文件中查看详细信息。 |
| 由于虚拟机未在 Windows 上运行 SQL Server 而跳过的 VM 数 | 由于虚拟机未运行 SQL Server 或由于它们不是 Windows 虚拟机而跳过的虚拟机数。 虚拟机以 `SubscriptionID, Resource Group, Virtual Machine` 格式列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>日志 

错误记录在名为 `VMsNotRegisteredDueToError<Timestamp>.log` 的日志文件中，其中时间戳是脚本启动的时间。 如果错误位于订阅级别，则日志包含逗号分隔的订阅 ID 和错误消息。 如果错误与虚拟机注册有关，则日志将包含订阅 ID、资源组名称、虚拟机名称、错误代码和消息，用逗号分隔。 

## <a name="remarks"></a>备注

使用提供的脚本将 SQL Server VM 注册到代理扩展时，请考虑以下事项：

- 注册到代理扩展时需要在 SQL Server VM 上运行的来宾代理。 Windows Server 2008 映像没有来宾代理，因此这些虚拟机将失败，并且必须使用 [NoAgent 管理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)手动注册。
- 可使用内置重试逻辑来克服透明错误。 如果虚拟机已成功注册，则它是一种快速操作。 但是，如果注册失败，则将重试每个虚拟机。  因此，虽然实际时间要求取决于错误的类型和数量，但你应留出大量时间来完成注册过程。 

## <a name="full-script"></a>完整脚本

有关 GitHub 上的完整脚本，请参阅[使用 Az PowerShell 批量注册 SQL Server VM](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)。 

复制完整脚本，并将其另存为 `RegisterSqLVMs.psm1`。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.yml)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)
