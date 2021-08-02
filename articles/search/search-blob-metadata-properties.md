---
title: 内容元数据属性
titleSuffix: Azure Cognitive Search
description: 文档的元数据属性可向搜索索引中的字段提供内容，或提供在运行时通知索引行为的信息。 本文列出了 Azure 认知搜索中支持的元数据属性。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: d77a0b53d14705d73b83d25ffe3355925355fdf1
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556615"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Azure 认知搜索中使用的内容元数据属性

SharePoint Online 和 Azure Blob 存储可以包含各种内容，其中很多内容类型的元数据属性都可用于编制索引。 正如可以为标准 blob 属性创建搜索字段一样（如 **`metadata_storage_name`** ），也可以为特定于文档格式的元数据属性创建字段。

## <a name="supported-document-formats"></a>支持的文档格式

认知搜索支持以下文档格式的 blob 索引和 SharePoint Online 文档索引：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>按文档格式划分的属性

下表汇总了针对每种文档格式执行的处理，并说明了 blob 索引器和 SharePoint Online 索引器提取的元数据属性。

| 文档格式/内容类型 | 提取的元数据 | 处理详细信息 |
| --- | --- | --- |
| HTML（text/html 或 application/xhtml+xml） |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |剥离 HTML 标记并提取文本 |
| PDF（应用程序/pdf） |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`<br/>`metadata_creation_date` |提取文本，包括嵌入的文档（不包括图像） |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOCM（应用程序/vnd.ms-word.document.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| WORD XML（应用程序/vnd.ms-word2006ml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |剥离 XML 标记并提取文本 |
| WORD 2003 XML（应用程序/vnd.ms-wordml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |剥离 XML 标记并提取文本 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLSM（应用程序/vnd.ms-excel.sheet.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPTM（应用程序/vnd.ms-powerpoint.presentation.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |提取文本，包括从附件中提取的文本。 `metadata_message_to_email`、`metadata_message_cc_email` 和 `metadata_message_bcc_email` 是字符串集合，其余字段是字符串。|
| ODT（应用程序/vnd.oasis.opendocument.text） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| ODS（应用程序/vnd.oasis.opendocument.spreadsheet） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| ODP（应用程序/vnd.oasis.opendocument.presentation） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| ZIP (application/zip) |`metadata_content_type` |从存档中的所有文档提取文本 |
| GZ（应用程序/gzip） |`metadata_content_type` |从存档中的所有文档提取文本 |
| EPUB（应用程序/epub+zip） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |从存档中的所有文档提取文本 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |剥离 XML 标记并提取文本 |
| KML (application/vnd.google-earth.kml+xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |剥离 XML 标记并提取文本 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |提取文本<br/>注意：如果需要从 JSON Blob 提取多个文档字段，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)了解详细信息 |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |提取文本，包括附件 |
| RTF（应用程序/rtf） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 提取文本|
| 纯文本 (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> | 提取文本|
| CSV (text/csv) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 提取文本<br/>注意：如果需要从 CSV Blob 提取多个文档字段，请参阅[为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)了解详细信息 |

## <a name="see-also"></a>另请参阅

* [Azure 认知搜索中的索引器](search-indexer-overview.md)
* [使用 AI 了解 blob](search-blob-ai-integration.md)
* [Blob 索引概述](search-blob-storage-integration.md)
* [SharePoint Online 索引](search-howto-index-sharepoint-online.md)