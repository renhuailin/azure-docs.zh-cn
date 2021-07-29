---
title: 使用渲染功能
description: 如何使用 Azure Batch 渲染功能。 尝试使用 Batch Explorer 应用程序，可以直接使用，也可以从客户端应用程序插件调用。
author: mscurrell
ms.author: markscu
ms.date: 03/12/2020
ms.topic: how-to
ms.openlocfilehash: d164eb0250c98573e781b87be339748900c4920b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110452059"
---
# <a name="using-azure-batch-rendering"></a>使用 Azure Batch 渲染

> [!IMPORTANT]
> 渲染 VM 映像和即用即付许可[已弃用，将于 2024 年 2 月 29 日停用](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)。 若要使用 Batch 进行渲染，[应使用自定义 VM 映像和标准应用程序许可。](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

可通过多种方式来使用 Azure Batch 渲染：

* API：
  * 使用任何 Batch API 编写代码。  开发人员可将 Azure Batch 功能集成到其现有应用程序或工作流（不管是在云中还是本地）中。
* 命令行工具：
  * 可以使用 [Azure 命令行](/cli/azure/)或 [PowerShell](/powershell/azure/) 来编写 Batch 脚本。
  * 具体而言，[Batch CLI 模板支持](./batch-cli-templates.md)可以简化创建池和提交作业的操作。
* Batch Explorer UI：
  * [Batch Explorer](https://github.com/Azure/BatchLabs) 是一个跨平台客户端工具，使用它也可以管理和监视 Batch 帐户。
  * 针对每个渲染应用程序，我们提供了许多的池和作业模板用于轻松创建池及提交作业。  应用程序 UI 中列出了一组模板，可从 GitHub 访问模板文件。
  * 可以从头创作自定义模板，或者，可以复制并修改 GitHub 中提供的模板。
* 客户端应用程序插件：
  * 借助提供的插件，可以直接从客户端设计和建模应用程序内部使用 Batch 渲染。  插件主要调用 Batch Explorer 应用程序，提供有关当前 3D 模型的上下文信息，并包含用于帮助管理资产的功能。

对于非开发人员或者非 Azure 专家级的最终用户而言，体验 Azure Batch 渲染的最佳、最简单的方法是直接使用或者从客户端应用程序插件调用 Batch Explorer 应用程序。

## <a name="using-batch-explorer"></a>使用 Batch Explorer

有适用于 Windows、OSX 和 Linux 的 Batch Explorer [可供下载](https://azure.github.io/BatchExplorer/)。

### <a name="using-templates-to-create-pools-and-run-jobs"></a>使用模板来创建池和运行作业

我们提供了一套综合性的模板用于配合 Batch Explorer，这样就可以轻松地为各种渲染应用程序创建池和提交作业，而无需指定在 Batch 中直接创建池、作业和任务时所需的全部属性。  Batch Explorer 提供的模板存储并显示在 [GitHub 存储库](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)中。

![Batch Explorer 库](./media/batch-rendering-using/batch-explorer-gallery.png)

提供的模板可以配合市场渲染 VM 映像中所有应用程序。  每个应用程序有多个模板，包括用于配合 CPU 和 GPU 池、Windows 和 Linux 池的池模板；作业模板包括完整的框架或平铺式 Blender 渲染，以及 V-Ray 分布式渲染。 提供的模板集将不断扩展，以配合其他 Batch 功能，例如池自动缩放。

此外，可以从头开始或者通过修改提供的模板，来生成自定义模板。 在 Batch Explorer 的“库”部分中选择“本地模板”项即可使用自定义模板。

### <a name="file-system-and-data-movement"></a>文件系统和数据移动

使用 Batch Explorer 中的“数据”部分，可以在本地文件系统与 Azure 存储帐户之间复制文件。

## <a name="next-steps"></a>后续步骤

* 了解如何将[渲染应用程序与 Batch 一起使用](batch-rendering-applications.md)。
* 了解[用于渲染资产和输出文件的存储与数据移动选项](batch-rendering-storage-data-movement.md)。
