---
title: 排查 Azure 导入/导出中的导入和导出问题 | Microsoft Docs
description: 了解如何处理使用 Azure 导入/导出时遇到的常见问题。
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 16b805f909ad06397894c5c405027986ff5fd939
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709339"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>排查 Azure 导入/导出中的问题
本文介绍如何排查在 Azure 导入/导出中导入和导出数据时遇到的常见问题。

## <a name="a-copy-session-failed-what-i-should-do"></a>复制会话失败。 我该怎么办？  

如果复制会话失败，可以采用两种做法：  
* 如果该错误可以通过重试来解决 - 例如，网络共享短期处于脱机状态，而现在已重新联机 - 则可以恢复复制会话。
* 如果该错误不可以通过重试来解决 - 例如，在命令行参数中指定了错误的源文件目录 - 则需要中止复制会话。
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>无法恢复或中止复制会话。

如果该复制会话是驱动器的第一个复制会话，错误消息应会指出：“无法恢复或中止第一个复制会话”。 在此情况下，可以删除旧日记文件，并重新运行该命令。  

如果复制会话不是驱动器的第一个复制会话，则始终可以恢复或中止该会话。  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>日记文件丢失。 是否仍然可以创建作业？

驱动器的日志文件包含数据复制的完整会话信息。 将文件添加到驱动器时，会使用日志文件来创建导入作业。 如果日记文件丢失，则需要为驱动器重做所有复制会话。

## <a name="next-steps"></a>后续步骤

<!--* [Set up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md)-->
* [为导入作业准备硬盘驱动器](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [使用复制日志文件查看作业状态](storage-import-export-tool-reviewing-job-status-v1.md)
<!--* [Repair an import job](storage-import-export-tool-repairing-an-import-job-v1.md)-->
<!--* [Repair an export job](storage-import-export-tool-repairing-an-export-job-v1.md)-->