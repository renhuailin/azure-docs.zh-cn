---
title: 导入和导出数据参考 - QnA Maker
description: 利用此导入和导出参考，以最佳方式执行知识库备份、存储和替换。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: d73fd6c7f49c10c8aca1060e91fc69b0e581738b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97707497"
---
# <a name="import-and-export-data-reference"></a>导入和导出数据参考

查看此导入和导出参考，以最佳方式执行知识库备份、存储和替换。

## <a name="import-and-export-knowledge-base"></a>导入和导出知识库

只能通过从 QnA Maker 门户的“设置”页导入文件来使用导出的知识库中的 TSV 和 XLS 文件 。 在知识库创建期间无法将其作为数据源使用，也无法通过“设置”页上的“+ 添加文件”或“+ 添加 URL”功能使用  。 

通过这些 TSV 和 XLS 文件导入知识库时，QnA 对将添加到编辑源中，而不是添加到从导出的知识库中提取 QnA 的源中。 
