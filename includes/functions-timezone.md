---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026636"
---
CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 

此设置的值取决于运行函数应用的操作系统和计划。

|操作系统 |计划 |值 |
|-|-|-|
| **Windows** |All | 将值设置为所需时区的名称，由 Windows 命令给定的每个对中的第二行给定 `tzutil.exe /L` |
| **Linux** |高级<br/>专用 |将值设置为所需时区的名称，如 [tz 数据库](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)中所示。 |

> [!NOTE]
> Linux 消耗计划目前不支持 `WEBSITE_TIME_ZONE`。

例如，美国东部时间 (由 `Eastern Standard Time` (Windows) 或 (Linux 表示， `America/New_York`) # A5 目前在标准时间04:00 和在夏令时期间使用 utc-05:00。 若要使计时器触发器每天凌晨10:00 触发，请创建名为的函数应用的应用设置 `WEBSITE_TIME_ZONE` ，将此值设置为 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) ，然后使用以下 NCRONTAB 表达式： 

```
"0 0 10 * * *"
``` 

当使用 `WEBSITE_TIME_ZONE` 时间时，将针对特定时区中的时间更改（包括夏令时和标准时间更改）进行调整。
