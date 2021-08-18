---
title: 在 Azure VM 中运行的 SQL Server 的 Azure 备份
description: 本文介绍如何将 Azure 备份注册到在 Azure 虚拟机中运行的 SQL Server。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b63e5c7de8a7198d5631075e9748d13f72ddb354
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294042"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>在 Azure VM 中运行的 SQL Server 的 Azure 备份

除了其他产品/服务，Azure 备份还提供在 Azure VM 中运行的 SQL Server 等工作负荷的备份支持。 由于 SQL 应用程序在 Azure VM 中运行，因此备份服务需要有权访问该应用程序并提取必需的详细信息。
为此，Azure 备份会在用户触发的注册过程中在 SQL Server 运行时所在的 VM 上安装 **AzureBackupWindowsWorkload** 扩展。

## <a name="prerequisites"></a>先决条件

有关支持的方案的列表，请参阅 Azure 备份支持的[可支持性矩阵](../../backup/sql-support-matrix.md#scenario-support)。

## <a name="network-connectivity"></a>网络连接

Azure 备份支持 NSG 标记，可以部署代理服务器或列出的 IP 范围；有关每种方法的详细信息，请参阅此[文章](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)。

## <a name="extension-schema"></a>扩展架构

扩展架构和属性值是服务传递给 CRP API 的配置值（运行时设置）。 这些配置值在注册和升级过程中使用。 **AzureBackupWindowsWorkload** 扩展也使用此架构。 此架构已预设置；可以在 objectStr 字段中添加新参数

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

以下 JSON 显示 WorkloadBackup 扩展的架构。  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>属性值

名称 | 值/示例 | 数据类型
 --- | --- | ---
区域设置 | zh-cn  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
commandStartTimeUTCTicks | "636967192566036845"  | 字符串
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>模板部署

建议在虚拟机上启用 SQL Server 备份，通过这种方式将 AzureBackupWindowsWorkload 扩展添加到虚拟机。 可以通过设计用于在 SQL Server VM 上自动进行备份的[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.recoveryservices/recovery-services-vm-workload-backup)来实现此操作。

## <a name="powershell-deployment"></a>PowerShell 部署

需要将包含 SQL 应用程序的 Azure VM“注册”到恢复服务保管库。 在注册期间，AzureBackupWindowsWorkload 扩展会安装到 VM 上。 请使用  [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) cmdlet 注册 VM。

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

此命令会返回此资源的“备份容器”，状态将为“已注册”。 

## <a name="next-steps"></a>后续步骤

- [详细了解](../../backup/backup-sql-server-azure-troubleshoot.md) Azure SQL Server 备份故障排除准则。
- 有关如何备份在 Azure 虚拟机 (VM) 上运行并使用 Azure 备份服务的 SQL Server 数据库的[常见问题](../../backup/faq-backup-sql-server.yml)。
