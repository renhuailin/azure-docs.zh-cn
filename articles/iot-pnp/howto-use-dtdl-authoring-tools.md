---
title: 使用工具创作和验证 DTDL 模型 | Microsoft Docs
description: 安装适用于 Visual Studio Code 或 Visual Studio 2019 的 DTDL 编辑器，并使用它创作 IoT 即插即用模型。
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 62a89fbb654624e1b1f9242d21d3bbb188140157
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067480"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>安装和使用 DTDL 创作工具

[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 模型是 JSON 文件。 可以使用 Visual Studio Code 或 Visual Studio 2019 的扩展来创作和验证这些模型文件。

## <a name="install-and-use-the-vs-code-extension"></a>安装和使用 VS Code 扩展

VS Code 的 DTDL 扩展添加了以下 DTDL 创作功能：

- DTDL v2 语法验证。
- Intellisense（包括自动完成功能）可帮助你使用语言语法。
- 从命令面板创建接口的功能。

若要安装 DTDL 扩展，请转到[适用于 Visual Studio Code 的 DTDL 编辑器](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。 还可以在 VS Code 的“扩展”视图中搜索 DTDL。

安装该扩展后，请使用它来帮助你在 VS Code 中创作 DTDL 模型文件：

- 该扩展在 DTDL 模型文件中提供语法验证，突出显示错误，如以下屏幕截图所示：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="VS Code 中的模型验证":::

- 编辑 DTDL 模型时，使用 IntelliSense 和自动完成功能：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="在 VS Code 中将 IntelliSense 用于 DTDL 模型":::

- 创建新的 DTDL 接口。 “DTDL: 创建接口”命令使用新接口创建一个 JSON 文件。 该接口包含示例遥测数据、属性和命令定义。

## <a name="install-and-use-the-visual-studio-extension"></a>安装和使用 Visual Studio 扩展

Visual Studio 2019 的 DTDL 扩展添加了以下 DTDL 创作功能：

- DTDL v2 语法验证。
- Intellisense（包括自动完成功能）可帮助你使用语言语法。

若要安装 DTDL 扩展，请转到 [VS 2019 的 DTDL 语言支持](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)。 你还可以在 Visual Studio 的“管理扩展”中搜索 DTDL。

安装该扩展后，请使用它来帮助你在 Visual Studio 中创作 DTDL 模型文件：

- 该扩展在 DTDL 模型文件中提供语法验证，突出显示错误，如以下屏幕截图所示：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Visual Studio 中的模型验证":::

- 编辑 DTDL 模型时，使用 IntelliSense 和自动完成功能：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="在 Visual Studio 中将 IntelliSense 用于 DTDL 模型":::

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何使用 Visual Studio Code 和 Visual Studio 2019 的 DTDL 扩展来创作和验证 DTDL 模型文件。 建议的下一步是了解如何将 [Azure IoT 浏览器与模型和设备](./howto-use-iot-explorer.md)配合使用。
