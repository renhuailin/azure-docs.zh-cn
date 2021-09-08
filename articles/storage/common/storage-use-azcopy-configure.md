---
title: 在 AzCopy（Azure 存储）中通过日志查找错误和恢复作业 | Microsoft Docs
description: 了解如何使用日志诊断错误，以及如何恢复使用计划文件暂停的作业。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 1f07907d3a4f421fa9f7a03c48d5f74496a1d45a
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303069"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>使用 AzCopy 中的日志和计划文件查找错误并恢复作业

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文将帮助你使用日志来诊断错误，然后使用计划文件来恢复作业。 本文还介绍了如何通过更改日志和计划文件的详细级别以及默认存储位置来配置日志和计划文件。

> [!NOTE]
> 如果你正在寻找 AzCopy 入门内容，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。 本文适用于 AzCopy V10，因为这是当前支持的 AzCopy 版本。 如果需要使用旧版 AzCopy，请参阅[使用旧版 AzCopy](storage-use-azcopy-v10.md#previous-version)。

## <a name="log-and-plan-files"></a>日志和计划文件

AzCopy 为每个作业创建日志和计划文件。  可以使用这些日志调查并解决任何潜在问题。 

日志将包含失败状态（`UPLOADFAILED`、`COPYFAILED` 和 `DOWNLOADFAILED`）、完整路径和失败的原因。

默认情况下，日志和计划文件位于 Windows 上的 `%USERPROFILE%\.azcopy` 目录中或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目录中，但可更改此位置。 

相关错误不一定是文件中出现的第一个错误。 对于网络错误、超时和服务器忙等错误，AzCopy 将重试最多 20 次，通常重试过程会成功。  你看到的第一个错误可能是已成功重试的无害内容。  因此，请查找 `UPLOADFAILED`、`COPYFAILED` 或 `DOWNLOADFAILED` 附近的错误，而不是查看文件中的第一个错误。 

> [!IMPORTANT]
> 向 Microsoft 支持部门提交请求时（或者排查涉及第三方的问题时），请告知你想执行的命令的修正版本。 这可以确保不会意外地与任何人共享 SAS。 可以在日志文件的开头找到经修订的版本。

## <a name="review-the-logs-for-errors"></a>查看日志中的错误

以下命令从 `04dc9ca9-158f-7945-5933-564021086c79` 日志中获取 `UPLOADFAILED` 状态的所有错误：

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>查看和恢复作业

每个传输操作都将创建一个 AzCopy 作业。 使用以下命令查看作业的历史记录：

```
azcopy jobs list
```

若要查看作业统计信息，请使用以下命令：

```
azcopy jobs show <job-id>
```

若要按状态筛选传输，请使用以下命令：

```
azcopy jobs show <job-id> --with-status=Failed
```

使用以下命令恢复失败/取消的作业。 此命令使用该作业的标识符以及 SAS 令牌，因为它不是持久性的（出于安全方面的原因）：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>" --destination-sas="<sas-token>"
```

> [!TIP]
> 用单引号 ('') 将路径参数（如 SAS 令牌）括起来。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

恢复某个作业时，AzCopy 会查看作业计划文件。 该计划文件列出了首次创建该作业时标识为待处理的所有文件。 恢复某个作业时，AzCopy 会尝试传输计划文件中列出的且尚未传输的所有文件。

## <a name="change-the-location-of-plan-files"></a>更改计划文件的位置

使用以下任何命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | PowerShell：`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> 在命令提示符处使用 `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，则计划文件将写入默认位置。

## <a name="change-the-location-of-log-files"></a>更改日志文件的位置

使用以下任何命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | PowerShell：`$env:AZCOPY_LOG_LOCATION="<value>"` <br> 在命令提示符处使用 `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，则日志将写入默认位置。

## <a name="change-the-default-log-level"></a>更改默认日志级别

AzCopy 日志级别默认设置为 `INFO`。 若要降低日志详细程度以节省磁盘空间，请使用 ``--log-level`` 选项覆盖此设置。 

可用的日志级别：`NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC` 和 `FATAL`。

## <a name="remove-plan-and-log-files"></a>删除计划和日志文件

若要从本地计算机中删除所有计划和日志文件以节省磁盘空间，请使用 `azcopy jobs clean` 命令。

若要删除只与一个作业关联的计划和日志文件，请使用 `azcopy jobs rm <job-id>`。 请将此示例中的 `<job-id>` 占位符替换为作业的作业 ID。

## <a name="see-also"></a>另请参阅

- [AzCopy 入门](storage-use-azcopy-v10.md)
