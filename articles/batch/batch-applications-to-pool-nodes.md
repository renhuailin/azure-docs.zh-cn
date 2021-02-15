---
title: 将应用程序和数据复制到池节点
description: 了解如何将应用程序和数据复制到池节点。
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: a5933a1c52e2848b6b414f1750bb24515fb9f28a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378496"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>将应用程序和数据复制到池节点

Azure Batch 支持多种方法，用于将数据和应用程序获取到计算节点，以便任务可以使用它们。

您选择的方法可能取决于您的文件或应用程序的范围。 可能需要数据和应用程序运行整个作业，因此需要在每个节点上安装数据和应用程序。 某些文件或应用程序可能仅对特定任务是必需的。 可能需要为作业安装其他程序，但不需要在每个节点上安装。 Batch 为上述每种场景都提供了相关工具。

## <a name="determine-the-scope-required-of-a-file"></a>确定文件所需的范围

需要确定文件的范围，即需要文件的是池、作业还是任务。 范围设为池的文件应使用池应用程序包或启动任务。 范围设为作业的文件应使用作业准备任务。 范围设在池或作业级别的文件的一个很好的例子就是应用程序。 范围设为任务的文件应使用任务资源文件。

## <a name="pool-start-task-resource-files"></a>池启动任务资源文件

对于需要在池中的每个节点上安装的应用程序或数据，请使用池启动任务资源文件。 将此方法与 [应用程序包](batch-application-packages.md) 或启动任务的资源文件集合一起使用，以便执行安装命令。  

例如，你可以使用 "启动任务" 命令行来移动或安装应用程序。 还可以指定 Azure 存储帐户中的文件或容器的列表。 有关详细信息，请参阅 [Add # ResourceFile IN REST 文档](/rest/api/batchservice/pool/add#resourcefile)。

如果在池上运行的每个作业运行的应用程序 ( 必须首先使用 .msi 文件安装) ，则需要将启动任务的 " **等待成功** " 属性设置为 " **true**"。 有关详细信息，请参阅 [Add # StartTask IN REST 文档](/rest/api/batchservice/pool/add#starttask)。

## <a name="application-package-references"></a>应用程序包引用

对于需要在池中的每个节点上安装的应用程序或数据，请考虑使用 [应用程序包](batch-application-packages.md)。 没有与应用程序包关联的安装命令，但你可使用启动任务来运行任何安装命令。 如果应用程序无需安装或者包含大量文件，则可使用此方法。

当你有大量文件时，应用程序包非常有用，因为它们可以将许多文件引用组合到小型有效负载中。 如果尝试将 100 个以上单独的资源文件包含到一个任务中，Batch 服务可能会遇到单任务内部系统限制。 当你有多个不同版本的相同应用程序，并且需要在它们之间进行选择时，应用程序包也很有用。

## <a name="extensions"></a>扩展

[扩展](create-pool-extensions.md) 是一种小型应用程序，用于简化批处理计算节点上的预配配置和设置。 创建池时，可以选择在预配计算节点时安装支持的扩展。 之后，扩展可以执行其预期操作。

## <a name="job-preparation-task-resource-files"></a>作业准备任务资源文件

对于必须安装的应用程序或数据才能运行，但不需要将其安装在整个池上，请考虑使用 [作业准备任务资源文件](./batch-job-prep-release.md)。

例如，如果你的池有许多不同类型的作业，并且只有一种作业类型需要 .msi 文件才能运行，则有必要将安装步骤放入作业准备任务。

## <a name="task-resource-files"></a>任务资源文件

当你的应用程序或数据只与单个任务相关时，会使用任务资源文件。

例如，你可能有五个任务，每个任务处理不同文件，然后在这种情况下将输出写入到 blob 存储，因为每个任务都有其自己的输入文件。

## <a name="additional-ways-to-get-data-onto-nodes"></a>将数据获取到节点的其他方法

由于可以控制 Azure Batch 节点，并且可以运行自定义可执行文件，因此可以从任意数量的自定义源中拉取数据。 请确保该批节点已连接到目标，并且该节点上有该源的凭据。

以下是将数据传输到 Batch 节点的几个示例：

- 从 SQL 下载数据
- 从其他 Web 服务/自定义位置下载数据
- 映射网络共享

## <a name="azure-storage"></a>Azure 存储

请记住，blob 存储具有下载可伸缩性目标。 对单个 Blob 来说，Azure 存储文件共享可伸缩性目标是相同的。 大小会影响所需的节点和池的数量。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在批处理中使用应用程序包](batch-application-packages.md)。
- 了解有关使用 [节点和池的](nodes-and-pools.md)详细信息。
