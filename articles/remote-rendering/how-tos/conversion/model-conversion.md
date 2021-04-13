---
title: 模型转换
description: 描述转换模型以进行渲染的过程
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318052"
---
# <a name="convert-models"></a>转换模型

使用 Azure 远程渲染可以呈现非常复杂的模型。 若要获得最大性能，必须对数据进行预处理，使其具有最佳格式。 根据数据量，此步骤可能需要一定的时间。 如果这段时间花在模型加载期间，则是不切实际的。 而且，对多个会话重复此过程将是一种浪费。 由于这些原因，ARR 服务提供了一个专用的转换服务，你可以提前运行该服务。
模型在进行转换后可以从 Azure 存储帐户加载。

## <a name="supported-source-formats"></a>支持的源格式

转换服务支持以下格式：

- **FBX**（版本 2011 到版本 2020）
- **GLTF**/**GLB**（版本 2.x）

格式之间在材料属性转换方面存在细微差异，如[模型格式的材料映射](../../reference/material-mapping.md)一章所列。

## <a name="the-conversion-process"></a>转换过程

1. [准备两个 Azure Blob 存储容器](blob-storage.md)：一个用于输入，一个用于输出
1. 将模型上传到输入容器（在子路径下为可选）
1. 通过[模型转换 REST API](conversion-rest-api.md) 触发转换过程
1. 轮询该服务以获取转化进度
1. 完成后，便会从链接的存储帐户加载模型
    - （请参阅[创建帐户](../create-an-account.md#link-storage-accounts)中的“链接存储帐户”步骤以链接存储帐户），
    - 或者通过提供“共享访问签名(SAS)”来加载模型。

所有模型数据（输入和输出）都存储在用户提供的 Azure blob 存储中。 Azure 远程渲染让你可以完全控制资产管理。

## <a name="pricing"></a>定价

有关转换定价的信息，请参阅[远程渲染定价](https://azure.microsoft.com/pricing/details/remote-rendering)页。


## <a name="conversion-parameters"></a>转换参数

有关各种转换选项，请参阅[此章节](configure-model-conversion.md)。

## <a name="examples"></a>示例

- [快速入门：转换模型以进行渲染](../../quickstarts/convert-model.md)是有关如何转换模型的分步说明。
- [示例 PowerShell 脚本](../../samples/powershell-example-scripts.md)（演示了转换服务的用法）可以在 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的“Scripts”文件夹中找到。

## <a name="next-steps"></a>后续步骤

- [将 Azure Blob 存储用于模型转换](blob-storage.md)
- [模型转换 REST API](conversion-rest-api.md)
- [配置模型转换](configure-model-conversion.md)
- [为转换设计文件布局](layout-files-for-conversion.md)
- [模型格式的材料映射](../../reference/material-mapping.md)
