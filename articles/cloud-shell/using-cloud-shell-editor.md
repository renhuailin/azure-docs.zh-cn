---
title: 使用 Azure Cloud Shell 编辑器 | Microsoft Docs
description: 概述如何使用 Azure Cloud Shell 编辑器。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832329"
---
# <a name="using-the-azure-cloud-shell-editor"></a>使用 Azure Cloud Shell 编辑器

Azure Cloud Shell 包括从开源 [Monaco 编辑器](https://github.com/Microsoft/monaco-editor)生成的集成文件编辑器。 Cloud Shell 编辑器支持语言突出显示、命令面板和文件资源管理器等功能。

![Cloud Shell 编辑器](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>打开编辑器

若要创建和编辑简单文件，可在 Cloud Shell 终端中运行 `code .` 来启动编辑器。 利用终端中设置的活动工作目录，该操作会打开编辑器。

若要直接打开文件以进行快速编辑，请运行 `code <filename>` 以便在不使用文件资源管理器的情况下打开编辑器。

若要通过 UI 按钮打开编辑器，请从工具栏单击 `{}` 编辑器图标。 这将打开编辑器，并将文件资源管理器默认到 `/home/<user>` 目录。

## <a name="closing-the-editor"></a>关闭编辑器

若要关闭编辑器，请打开编辑器右上角的 `...` 操作面板，然后选择 `Close editor`。

![关闭编辑器](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>命令面板

若要启动命令面板，请在焦点位于编辑器上时使用 `F1`。 此外还可通过操作面板打开命令面板。

![Cmd 调色板](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>用于 Monaco 编辑器

通过 [Monaco 编辑器](https://github.com/Microsoft/monaco-editor)使用 Monarch 语法定义中的上游功能支持 Cloud Shell 编辑器中的语言突出显示支持。 若要了解如何做出贡献，请阅读 [Monaco 参与者指南](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)。

## <a name="next-steps"></a>后续步骤

- [试用 Cloud Shell 中 Bash 的快速入门](quickstart.md)
- [查看集成的 Cloud Shell 工具的完整列表](features.md)
