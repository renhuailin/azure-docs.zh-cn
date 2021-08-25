---
title: 语音 CLI 配置选项 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何创建和管理配置文件来与 Azure 语音 CLI 一起使用。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: lajanuar
ms.openlocfilehash: eeff86e7955f2e8e32059ba1acb220d0dd2a567f
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071515"
---
# <a name="speech-cli-configuration-options"></a>语音 CLI 配置选项

语音 CLI 的行为可依赖于配置文件中的设置，你可使用 `@` 符号引用这些设置。 语音 CLI 会将新设置保存在新的 `./spx/data` 子目录中，该目录是当前工作目录中为语音 CLI 创建的。 查找配置值时，语音 CLI 将搜索当前工作目录，再在 `./spx/data` 的数据存储中搜索，然后在其他数据存储（包括 `spx` 二进制文件中的最终只读数据存储）中搜索。 

在语音 CLI 快速入门中，你使用了数据存储来保存 `@key` 和 `@region` 值，因此当时你无需通过每个 `spx` 命令来指定它们。 请记住，你可使用配置文件来存储你自己的配置设置，甚至使用它们来传递 URL，或者传递在运行时生成的其他动态内容。

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>在数据存储中创建和管理配置文件

本部分介绍了如何使用本地数据存储中的配置文件借助 `spx config` 来存储和提取命令设置，以及如何使用 `--output` 选项存储来自语音 CLI 的输出。

下面的示例将清除 `@my.defaults` 配置文件，为文件中的“键”和“区域”添加键值对，并在调用 `spx recognize` 时使用此配置 。

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

你还可以向配置文件写入动态内容。 例如，以下命令将创建一个自定义语音模型，并在配置文件中存储新模型的 URL。 下一条命令要等到该 URL 的模型可以使用时才返回。

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

以下示例将两条 URL 写入 `@my.datasets.txt` 配置文件。 在此方案中，`--output` 可以包括一个可选“添加”关键字，以创建配置文件或追加到现有配置文件。


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

有关数据存储文件的详细信息，包括使用默认配置文件（用于命令特定默认设置的 `@spx.default`、`@default.config` 和 `@*.default.config`），请输入以下命令：

```console
spx help advanced setup
```

## <a name="next-steps"></a>后续步骤 

* [使用语音 CLI 的批处理操作](./spx-batch-operations.md)