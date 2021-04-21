---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028537"
---
使用以下方法在首选的开发环境中添加支持。

| 开发环境  | 应用程序类型      | 添加支持 |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# 类库      | [安装 NuGet 包](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | 基于[核心工具](../articles/azure-functions/functions-run-local.md) | [注册扩展捆绑包](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>建议安装 [ 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)。 |
| 任何其他编辑器/IDE     | 基于[核心工具](../articles/azure-functions/functions-run-local.md) | [注册扩展捆绑包](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure 门户             | 在门户中只能联机使用 | 添加绑定时安装<br /><br /> 请参阅[更新扩展](../articles/azure-functions/functions-bindings-register.md)，更新现有绑定扩展而不必重新发布函数应用。 |