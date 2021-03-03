---
title: Azure 存储资源管理器命令行选项 |Microsoft Docs
description: Azure 存储资源管理器启动命令行选项的文档
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744406"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure 存储资源管理器命令行选项

Microsoft Azure 存储资源管理器包含一组可在启动应用程序时添加的命令行选项。 大多数命令行选项用于调试或故障排除。

## <a name="command-line-options"></a>命令行选项
选项  | 说明
:------- | :-----------
`--debug`/`--prod`  | 在调试或生产模式下启动应用程序。 在调试模式中，本地附件数据将存储在应用程序的本地存储中，而不会被加密。 隐藏的属性将显示在所选资源节点的 "属性" 面板中。 日志详细级别将设置为 "打印调试消息存储资源管理器的内部安装逻辑。 默认值为 `--prod`。
`--lang`  | 使用给定的语言启动该应用程序。 例如 `--lang="zh-Hans"`。
`--disable-gpu` | 启动不带 GPU 加速的应用程序。
`--auto-open-dev-tools` | 浏览器窗口显示后，让应用程序立即打开 "开发人员工具" 窗口。 如果要在浏览器窗口的启动代码行中命中断点，则此选项很有用。
`--verbosity` | 设置存储资源管理器日志记录的详细级别。 支持的详细级别包括 `debug` 、 `verbose` 、、 `info` `warn` 、 `error` 和 `silent` 。 例如 `--verbosity=verbose`。 在生产模式下运行时，默认详细级别为 `info` 。 在调试模式下运行时，日志详细级别将始终为 `debug` 。
`--log-dir` | 设置用于保存日志文件的目录。 例如 `--log-dir=path_to_a_directory`。

使用自定义命令行选项开始存储资源管理器的示例

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> 在新的存储资源管理器版本中，这些命令行选项可能会发生变化。

## <a name="when-to-use-command-line-options"></a>何时使用命令行选项

一些命令行选项可用于自定义存储资源管理器。 对于具有相应用户设置的选项（如） `--lang` 。 建议使用用户设置，而不是使用命令行选项。 

其他命令行选项可用于调试和故障排除。 如果在存储资源管理器中遇到问题，请在调试模式下重现问题，以帮助我们获取更详细的信息进行调查。