---
title: 在 Azure 导入/导出中查看导入和导出中的复制日志 | Microsoft Docs
description: 了解如何为数据复制、上传问题查看导入和导出中的错误/复制日志。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709247"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>通过 Azure 导入/导出查看导入和导出中的复制日志
当 Microsoft Azure 导入/导出服务处理与导入或导出作业关联的驱动器时，它会将副本日志文件写入到用于导入或导出 blob 的存储帐户。 

该日志文件包含与已导入或导出的每个文件相关的详细状态。 

当你查询已完成作业的状态时，该服务将返回每个副本日志文件的 URL。 有关详细信息，请参阅[获取作业](/rest/api/storageimportexport/Jobs/Get)。  

## <a name="example-urls"></a>示例 URL

下面是包含两个驱动器的导入作业的复制日志文件的示例 URL：  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>后续步骤

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [为导入作业准备硬盘驱动器](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->