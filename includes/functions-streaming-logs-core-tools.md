---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020329"
---
#### <a name="built-in-log-streaming"></a>内置日志流式处理

使用 `logstream` 选项开始接收在 Azure 中运行的特定函数应用的流式处理日志，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>尚未在 Core Tools 中为在消耗计划中的 Linux 上运行的函数应用启用内置流式处理。 相反，对于这些托管计划，你需要使用实时指标流近乎实时地查看日志。

#### <a name="live-metrics-stream"></a>实时指标流

通过包含 `--browser` 选项，可在新的浏览器窗口中查看函数应用的[实时指标流](../articles/azure-monitor/app/live-stream.md)，如下例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
