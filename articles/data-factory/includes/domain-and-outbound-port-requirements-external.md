---
title: include 文件
description: include 文件
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389480"
---
| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | 使用分阶段复制功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 仅当从或向 Azure SQL 数据库或 Azure Synapse Analytics 复制时才是必需的，否则为可选。 在不打开端口 1433 的情况下，使用暂存复制功能将数据复制到 SQL 数据库或 Azure Synapse Analytics。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 仅当从/向 Azure Data Lake Store 复制时才是必需的，否则为可选。 |
