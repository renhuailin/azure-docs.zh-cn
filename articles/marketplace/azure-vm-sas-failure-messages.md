---
title: 虚拟机 SAS 失败消息-Azure Marketplace
description: 使用共享访问签名 (SAS) 时的常见问题。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126832"
---
# <a name="virtual-machine-sas-failure-messages"></a>虚拟机 SAS 失败消息

以下介绍使用共享访问签名（用来标识和共享解决方案的已上传 VHD）时遇到的常见问题以及建议的解决方法。

| 问题 | 失败消息 | Fix |
| --------- | ------------------- | ------- |
| *复制映像时失败* |  |  |
| 在 SAS URI 中未找到“?” | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用建议的工具更新 SAS URI。 |
| SAS URI 中不存在“st”和“se”参数 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 使用正确的“开始日期”和“结束日期”值更新 SAS URI。 |
| SAS URI 中不存在“sp=rl” | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI，将权限设置为 `Read` 和 `List`。 |
| SAS URI 的 VHD 名称中存在空格 | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 更新 SAS URI 以删除空格。 |
| SAS URI 授权错误 | `Failure: Copying Images. Not able to download blob due to authorization error.` | 检查并更正 SAS URI 格式。 必要时重新生成。 |
| SAS URI 的“st”和“se”参数没有完整的日期时间规范 | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI 的“开始日期”和“结束日期”参数（`st` 和 `se` 子字符串）必须具有完整的日期时间格式，例如 `11-02-2017T00:00:00Z`。 缩短版本无效（默认情况下，Azure CLI 中的某些命令可能会生成缩短的值）。 |
|  |  |  |

有关详细信息，请参阅[使用共享访问签名 (SAS)](../storage/common/storage-sas-overview.md)。

## <a name="next-steps"></a>后续步骤

- [生成 SAS URI](azure-vm-get-sas-uri.md)