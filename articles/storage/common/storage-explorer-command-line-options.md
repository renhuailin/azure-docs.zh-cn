---
title: Azure 存储资源管理器命令行选项 | Microsoft Docs
description: Azure 存储资源管理器启动命令行选项文档
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: f5c43822bb9b856126c9b1ff80212d457044887b
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449524"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure 存储资源管理器命令行选项

Microsoft Azure 存储资源管理器包含一组可在启动应用程序时添加的命令行选项。 大多数命令行选项用于调试或故障排除。

## <a name="command-line-options"></a>命令行选项
选项  | 说明
:------- | :-----------
`--debug`/`--prod`  | 在调试或生产模式下启动应用程序。 在调试模式中，本地附件数据将存储在应用程序的本地存储中，不会被加密。 隐藏的属性将显示在所选资源节点的“属性”面板中。 日志详细程度将设置为打印调试消息，从而显示存储资源管理器的内部设置逻辑。 默认值为 `--prod`。
`--lang`  | 使用给定的语言启动应用程序。 例如，`--lang="zh-Hans"`。
`--disable-gpu` | 启动不带 GPU 加速的应用程序。
`--auto-open-dev-tools` | 显示浏览器窗口后，让应用程序立即打开开发人员工具窗口。 如果要在浏览器窗口的启动代码的行中命中断点，则此选项很有用。
`--verbosity` | 设置存储资源管理器日志记录的详细程度。 支持的详细程度包括 `debug`、`verbose`、`info`、`warn`、`error` 和 `silent`。 例如，`--verbosity=verbose`。 在生产模式下运行时，默认详细程度为 `info`。 在调试模式下运行时，日志详细程度将始终为 `debug`。
`--log-dir` | 设置目录以保存日志文件。 例如，`--log-dir=path_to_a_directory`。

使用自定义命令行选项启动存储资源管理器的示例

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> 在新的存储资源管理器版本中，这些命令行选项可能会发生变化。

## <a name="when-to-use-command-line-options"></a>何时使用命令行选项

一些命令行选项可用于自定义存储资源管理器。 对于具有相应用户设置的选项，如 `--lang`。 建议使用用户设置，而不是使用命令行选项。 

其他命令行选项可用于调试和故障排除。 如果在存储资源管理器中遇到问题，在调试模式下重现问题可帮助我们获取更详细的信息进行调查。