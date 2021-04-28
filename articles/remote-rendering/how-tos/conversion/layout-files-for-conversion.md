---
title: 为转换设计文件布局
description: 将文件放入输入容器的最佳实践建议。
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: ab01143fcab5ceb5468ef8fde233905b8d3e05d1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124538"
---
# <a name="laying-out-files-for-conversion"></a>为转换设计文件布局

为了正确处理资产，转换服务需要能够找到所有输入文件。
这其中包括正在转换的主要资产文件，以及通常由该资产文件中的路径引用的其他一些文件。
针对资产转换请求提供了两个参数，用于确定转换服务查找这些文件的方式：`settings.inputLocation.blobPrefix`（可选）和 `settings.inputLocation.relativeInputAssetPath`。
它们完整记录在[转换 REST API](conversion-rest-api.md) 页中。
为设计文件布局，需要注意的重点是，`BlobPrefix` 将确定在处理资产时可用于转换服务的完整文件集。

> [!Note]
> 该服务将下载 input.BlobPrefix 下的所有文件。 确保文件名和路径不超过 [Windows 路径长度限制](/windows/win32/fileio/maximum-file-path-limitation)，以免出现服务问题。 

## <a name="placing-files-so-they-can-be-found"></a>放置文件以便可以找到它们

当源资产使用外部文件时，这些文件的路径将存储在该资产中。
转换服务必须在该资产的原始文件系统以外的文件系统中解释这些路径。
如果这些路径存储为相对路径，且源资产与其引用的文件之间的相对位置不变，则转换服务可以轻松找到引用的文件。

> [!Note]
> 建议将文件放入输入容器，使其相对位置与创建资产时它们所在的位置相同。

> [!Note]
> 创建带有相对路径的资产是首选。
> [设置 3DS Max 材料](../../tutorials/modeling/3dsmax-material-setup.md)教程提供了如何确保资产使用相对路径的 3DS Max 示例。

## <a name="finding-textures"></a>查找纹理

资产的生成方式多种多样，因此转换服务必须是灵活的。
特别是，它必须处理资产中的路径和纹理位置不完全匹配的情况。
例如，生成的资产中包含绝对路径，而这些路径永远不会与转换服务使用的文件系统相匹配。
若要处理这类情况，需使用最佳方法查找纹理。

查找纹理的算法是这样：假定一个存储在资产中的路径，查找最长的子路径后缀，该后缀在用作源资产位置的相对路径时，指向存在的目标文件。
如果这类子路径（包含整个路径）没有目标文件，则视为纹理缺失。

请考虑下面精心设计的文件系统： 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
如果 myAsset.fbx 使用相对路径 `..\Textures\MyAssetTextures\myTexture.png` 引用纹理，则转换服务将使用文件 E。事实上，它可以使用 A、C、E 中任意一个文件（如果存在），但文件 E 是首选，因为它拥有最长的后缀。
永远不会使用文件 B 和 D，因为 `Textures\myTexture.png` 不是存储路径的任何后缀的组成部分。
但如果资产包含路径 `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 或 `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png`，则转换服务能够查找到文件 A 和 C（如果存在，A 和 C 中首选 C）。 但是，这种方式无法找到 E，必须重新定位该文件。
将纹理文件夹移动到资产旁边，可以解决此问题。

> [!Note]
> 如果找不到纹理，则可能的补救措施是确保资产与包含纹理的一些子树是同级。

## <a name="next-steps"></a>后续步骤

- [模型转换](model-conversion.md)