---
title: 将应用程序和数据复制到池节点
description: 了解如何将应用程序和数据复制到池节点。
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d53a285319e46f9311684b0ad66c1a278750f8b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752089"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>将应用程序和数据复制到池节点

Azure Batch 支持通过多种方式将数据和应用程序获取到计算节点，以便在任务中使用它们。

用户可根据文件或应用程序的范围来选择方法。 运行整个作业可能都需要数据和应用程序，因此需要将它们安装到每个节点上。 有些文件或应用程序可能只是特定任务需要。 还有一些可能需要为了作业而安装，但并不需要在每个节点上都安装。 Batch 为上述每种场景都提供了相关工具。

## <a name="determine-the-scope-required-of-a-file"></a>确定文件所需的范围

需要确定文件的范围，即需要文件的是池、作业还是任务。 范围设为池的文件应使用池应用程序包或启动任务。 范围设为作业的文件应使用作业准备任务。 范围设在池或作业级别的文件的一个很好的例子就是应用程序。 范围设为任务的文件应使用任务资源文件。

## <a name="pool-start-task-resource-files"></a>池启动任务资源文件

对于需要在池中每个节点上都安装的应用程序或数据，请使用池启动任务资源文件。 结合使用这种方法以及[应用程序包](batch-application-packages.md)或启动任务的资源文件集合，才能执行安装命令。  

例如，可以使用启动任务命令行来移动或安装应用程序。 此外，也可以在 Azure 存储帐户中指定文件或容器的列表。 有关详细信息，请参阅 [REST 文档中的 Add#ResourceFile](/rest/api/batchservice/pool/add#resourcefile)。

如果池中运行的每个作业运行的都是必须先使用 .msi 文件安装的应用程序，则需要将启动任务的“等待成功”属性设置为“true”。 有关详细信息，请参阅 [REST 文档中的 Add#StartTask](/rest/api/batchservice/pool/add#starttask)。

## <a name="application-package-references"></a>应用程序包引用

对于需要在池中每个节点上都安装的应用程序或数据，可考虑使用[应用程序包](batch-application-packages.md)。 没有与应用程序包关联的安装命令，但你可使用启动任务来运行任何安装命令。 如果应用程序无需安装或者包含大量文件，则可使用此方法。

当有大量文件时，应用程序包非常有用，因为它们可以将许多文件引用组合到一个小型有效负载中。 如果尝试将 100 个以上单独的资源文件包含到一个任务中，Batch 服务可能会遇到单任务内部系统限制。 当有同一应用程序的多个不同版本并需要在它们之间进行选择时，应用程序包也很有用。

## <a name="extensions"></a>扩展

[扩展](create-pool-extensions.md)是一种小型应用程序，用于帮助在 Batch 计算节点上进行预配后配置和设置。 创建池时，可以选择在预配计算节点时安装支持的扩展。 之后，扩展就可执行其预期操作。

## <a name="job-preparation-task-resource-files"></a>作业准备任务资源文件

对于作业运行必须安装但无需安装到整个池的应用程序或数据，可考虑使用[作业准备任务资源文件](./batch-job-prep-release.md)。

例如，如果池中包含许多不同类型的作业，但只有一种类型的作业运行时需要 .msi 文件，则将安装步骤放到作业准备任务中比较合适。

## <a name="task-resource-files"></a>任务资源文件

当应用程序或数据仅关乎单个任务时，适合使用任务资源文件。

例如，用户可能有五个任务，每个任务处理不同的文件，再将输出写入 blob 存储；在这种情况下，则应对任务资源文件集合指定输入文件，因为每个任务都有自己的输入文件。

## <a name="additional-ways-to-get-data-onto-nodes"></a>将数据获取到节点的其他方法

由于用户可以控制 Azure Batch 节点并可运行自定义可执行文件，所以可以从任意数量的自定义源提取数据。 请确保 Batch 节点已连接到目标，并且你拥有节点上该源的凭据。

下面是将数据传输到 Batch 节点的几种方法示例：

- 从 SQL 下载数据
- 从其他 Web 服务/自定义位置下载数据
- 映射网络共享

## <a name="azure-storage"></a>Azure 存储

切记，Blob 存储具有下载可伸缩性目标。 对单个 Blob 来说，Azure 存储文件共享可伸缩性目标是相同的。 大小会影响所需的节点数和池数。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Batch 使用应用程序包](batch-application-packages.md)。
- 详细了解[使用节点和池](nodes-and-pools.md)。
