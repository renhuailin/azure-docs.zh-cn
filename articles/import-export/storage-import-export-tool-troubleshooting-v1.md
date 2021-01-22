---
title: 排查 Azure 导入/导出中的导入和导出问题 |Microsoft Docs
description: 了解如何处理使用 Azure 导入/导出时遇到的常见问题。
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706232"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>排查 Azure 导入/导出中的问题
本文介绍如何解决在 Azure 导入/导出中导入和导出数据时遇到的常见问题。

## <a name="a-copy-session-failed-what-i-should-do"></a>复制会话失败。 我应该做什么？  

复制会话失败时，有两个选项：  
* 如果可以重试该错误，例如，如果网络共享短时间处于脱机状态，而现在已重新联机，则可以恢复复制会话。
* 如果无法重试错误，例如，如果在命令行参数中指定了错误的源文件目录，则需要中止复制会话。
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>无法恢复或中止复制会话。

如果该复制会话是驱动器的第一个复制会话，错误消息应会指出：“无法恢复或中止第一个复制会话”。 在此情况下，可以删除旧日记文件，并重新运行该命令。  

如果复制会话不是驱动器的第一个会话，则始终可以恢复或中止该会话。  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>我丢失了日志文件。 是否仍可创建作业？

驱动器的日志文件包含数据复制的完整会话信息。 将文件添加到驱动器时，会使用日志文件来创建导入作业。 如果丢失了日志文件，则必须重做驱动器的所有复制会话。

## <a name="next-steps"></a>后续步骤

* [设置 Azure 导入/导出工具](storage-import-export-tool-setup-v1.md)
* [为导入作业准备硬盘驱动器](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [用复制日志文件查看作业状态](storage-import-export-tool-reviewing-job-status-v1.md)
* [修复导入作业](storage-import-export-tool-repairing-an-import-job-v1.md)
* [修复导出作业](storage-import-export-tool-repairing-an-export-job-v1.md)