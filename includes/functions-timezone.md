---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026636"
---
CRON 表达式使用的默认时区为协调世界时 (UTC)。 若要让 CRON 表达式基于其他时区，请为你的函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置。 

此设置的值取决于运行函数应用的操作系统和计划。

|操作系统 |计划 |值 |
|-|-|-|
| **Windows** |All | 将该值设置为所需时区的名称，由 Windows 命令 `tzutil.exe /L` 给定的每个对的第二行指定 |
| **Linux** |高级<br/>专用 |将值设置为所需时区的名称，如 [tz 数据库](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)中所示。 |

> [!NOTE]
> Linux 消耗计划目前不支持 `WEBSITE_TIME_ZONE`。

例如，美国东部时间（由 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux) 表示）当前在标准时间中使用 UTC-05:00，在夏令时中使用 UTC-04:00。 若要使计时器触发器每天在东部时间上午 10:00 触发，请为函数应用创建一个名为 `WEBSITE_TIME_ZONE` 的应用设置并将值设置为 `Eastern Standard Time` (Windows) 或 `America/New_York` (Linux)，然后使用以下 NCRONTAB 表达式： 

```
"0 0 10 * * *"
``` 

使用 `WEBSITE_TIME_ZONE` 时，时间将针对特定时区中的时间更改进行调整，包括夏令时和标准时间的更改。
