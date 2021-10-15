---
title: 管理 Azure 支持请求
description: 介绍如何查看支持请求、发送消息、更改请求严重性级别、与 Azure 支持共享诊断信息、重新打开已关闭的支持请求以及上传文件。
tags: billing
ms.topic: how-to
ms.date: 09/30/2021
ms.openlocfilehash: 8e7b074883fe2dcfb79913e54cf7180e26f29c2a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353195"
---
# <a name="manage-an-azure-support-request"></a>管理 Azure 支持请求

[创建 Azure 支持请求](how-to-create-azure-support-request.md)后，可以在 [Azure 门户](https://portal.azure.com)中对其进行管理，本文将对此进行介绍。 还可使用 [Azure 支持票证 REST API](/rest/api/support) 或通过使用 [Azure CLI](/cli/azure/azure-cli-support-request) 以编程方式创建和管理请求。

## <a name="view-support-requests"></a>查看支持请求

转到“帮助 + 支持” >  “所有支持请求”来查看支持请求的详细信息和状态 。

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="所有支持请求":::

可以在此页面上对支持请求进行搜索、筛选和排序。 选择支持请求以查看详细信息，包括其严重性以及与请求关联的任何消息。

## <a name="send-a-message"></a>发送消息

1. 在“所有支持请求”页上，选择支持请求。

1. 在“支持请求”页上，选择“新建消息” 。

1. 输入消息，然后选择“提交”。

## <a name="change-the-severity-level"></a>更改严重性级别

> [!NOTE]
> 最大严重性级别取决于[支持计划](https://azure.microsoft.com/support/plans)。

1. 在“所有支持请求”页上，选择支持请求。

1. 在“支持请求”页上，选择“更改” 。

1. Azure 门户显示两个屏幕中的一个，具体取决于请求是否已分配给支持工程师：

    - 如果未分配请求，将显示如下所示的屏幕。 选择新的严重性级别，然后选择“更改”。

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="选择新的严重性级别":::

    - 如果已分配请求，将显示如下所示的屏幕。 选择“确定”，然后创建[新消息](#send-a-message)以请求更改严重性级别。

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="无法选择新的严重性级别":::

## <a name="share-diagnostic-information-with-azure-support"></a>与 Azure 支持共享诊断信息

创建支持请求时，可以在“共享诊断信息”部分中选择“是”或“否”。   此选项确定了 Azure 支持是否可从你的 Azure 资源中收集[诊断信息](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)（例如[日志文件](how-to-create-azure-support-request.md#advanced-diagnostic-information-logs)），这些信息可能有助于解决你的问题。

若要在创建请求后更改“共享诊断信息”选择：

1. 在“所有支持请求”页上，选择支持请求。

1. 在“支持请求”页上，查找“共享诊断信息”，然后选择“更改”。

1. 选择“是”或“否”，然后选择“确定”进行确认。

    :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="为诊断信息授予权限":::

## <a name="upload-files"></a>上传文件

可以使用文件上传选项来上传诊断文件或你认为与支持请求相关的任何其他文件。

1. 在“所有支持请求”页上，选择支持请求。

1. 在“支持请求”页上，浏览查找文件，然后选择“上传” 。 如果有多个文件，请重复该过程。

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="上传文件":::

### <a name="file-upload-guidelines"></a>文件上传指南

使用文件上传选项时，请遵循以下指导原则：

- 为保护隐私，请不要在上传内容中含入任何个人信息。
- 文件名不得超过 110 个字符。
- 最多只能上传一个文件。
- 文件不得大于 4 MB。
- 所有文件必须具有文件扩展名，如 .docx 或 .xlsx 。 下表显示了允许上传的文件扩展名。

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | P1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zipped  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>关闭支持请求

若要关闭支持请求，请[发送一条消息](#send-a-message)要求关闭请求。

## <a name="reopen-a-closed-request"></a>重新打开已关闭的请求

若要重新打开已关闭的支持请求，请创建一条[新消息](#send-a-message)，该消息将自动重新打开该请求。

## <a name="cancel-a-support-plan"></a>取消支持计划

若要取消支持计划，请参阅[取消支持计划](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan)。

## <a name="next-steps"></a>后续步骤

- 请参阅[创建 Azure 支持请求](how-to-create-azure-support-request.md)的过程。
- 了解 [Azure 支持工单 REST API](/rest/api/support)。
