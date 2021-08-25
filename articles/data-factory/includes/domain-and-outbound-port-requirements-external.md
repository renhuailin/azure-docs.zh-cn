---
title: include 文件
description: include 文件
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: f738a72c4316b51d13cd93194576549839d8127f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638989"
---
| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | 使用分阶段复制功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 仅当从或向 Azure SQL 数据库或 Azure Synapse Analytics 复制时才是必需的，否则为可选。 在不打开端口 1433 的情况下，使用暂存复制功能将数据复制到 SQL 数据库或 Azure Synapse Analytics。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 仅当从/向 Azure Data Lake Store 复制时才是必需的，否则为可选。 |
