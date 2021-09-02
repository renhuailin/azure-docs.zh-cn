---
title: 使用模板端到端运行作业
description: 只需使用 CLI 命令便可创建池、上传输入数据、创建作业和关联的任务，以及下载生成的输出数据。
ms.topic: how-to
ms.date: 08/06/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ca7bb6e35cb289f435dc23c2e43af4fedb018524
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752093"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>使用 Azure Batch CLI 模板和文件传输

通过使用 Azure CLI 的批处理扩展，用户可以在不编写代码的情况下运行批处理作业。

通过 Azure CLI 创建 JSON 模板文件，并使用该模板文件创建批处理池、作业和任务。 使用 CLI 扩展命令轻松将作业输入文件上传到与 Batch 帐户关联的存储帐户，并下载作业输出文件。

> [!NOTE]
> JSON 文件不支持 [Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)所支持的功能。 它们应采用与原始 REST 请求正文相同的格式。 CLI 扩展不更改任何现有的命令，但它确实有一个添加部分 Azure 资源管理器模板功能的类似模板选项。 请参阅[适用于 Windows、Mac 和 Linux 的 Azure Batch CLI 扩展](https://github.com/Azure/azure-batch-cli-extensions)。

## <a name="overview"></a>概述

通过 Azure CLI 扩展，非开发者用户可使用端到端的 Batch。 只需使用 CLI 命令便可创建池、上传输入数据、创建作业和关联的任务，以及下载生成的输出数据。 不需要任何其他代码。 可以直接运行 CLI 命令，也可以将其集成到脚本中。

Batch 模板基于 [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) 中现有的 Batch 支持，在创建池、作业、任务和其他项时允许使用 JSON 文件来指定属性值。 Batch 模板添加了以下功能：

- 可以定义参数。 使用模板时，仅指定参数值以创建项，而在模板正文中指定其他项属性值。 了解 Batch 和 Batch 运行的应用程序的用户可以创建模板、指定池、作业和任务属性值。 不太熟悉 Batch 和/或应用程序的用户只需指定定义的参数的值。

- 使用作业任务工厂可创建与作业相关联的一个或多个任务，而无需创建多个任务定义，因此极大地简化了作业的提交。

作业通常使用输入数据文件并产生输出数据文件。 默认情况下，每个 Batch 帐户都与一个存储帐户相关联。 可以使用 Azure CLI 将文件传输到此存储帐户以及从此存储帐户传输文件，不需要进行编码，也不需要任何存储凭据。

例如，[ffmpeg](https://ffmpeg.org/) 是处理音频和视频文件的常用应用程序。 使用 Azure Batch CLI 扩展，可让用户更轻松地调用 ffmpeg 以将源视频文件转码为不同的分辨率。 进程可能如下所示：

- 创建池模板。 创建模板的用户了解如何调用 ffmpeg 应用程序及其需求；他们可指定适当的操作系统、VM 大小、安装 ffmpeg 的方法（例如，从应用程序包或使用包管理器进行安装），以及其他池属性值。 已创建参数，因此当使用模板时，仅需指定池 ID 和 VM 数。
- 创建作业模板。 创建模板的用户了解如何调用 ffmpeg，以便将源视频转码为不同的分辨率，并指定任务命令行；他们还了解存在包含源视频文件（每个输入文件均包含所需任务）的文件夹。
- 具有一组要转码的视频文件的最终用户首先需要使用池模板创建一个池，然后仅指定池 ID 和所需的 VM 数量。 然后，他们可以上传源文件以进行转码。 可使用作业模板提交作业，仅指定池 ID 和上传的源文件的位置。 创建 Batch 作业时，每个输入文件生成一项任务。 最后，可以下载已转码的输出文件。

## <a name="installation"></a>安装

若要安装 Azure Batch CLI 扩展，请首先[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)，或在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行 Azure CLI。

使用以下 Azure CLI 命令安装最新版本的 Batch 扩展：

```azurecli
az extension add --name azure-batch-cli-extensions
```

有关 Batch CLI 扩展和其他安装选项的详细信息，请参阅 [GitHub 存储库](https://github.com/Azure/azure-batch-cli-extensions)。

若要使用 CLI 扩展功能，需要 Azure Batch 帐户和链接的存储帐户（针对于在存储之间传输文件的命令）。

要使用 Azure CLI 登录 Batch 帐户，请参阅[使用 Azure CLI 管理 Batch 资源](batch-cli-get-started.md)。

## <a name="templates"></a>模板

Azure Batch 模板在功能和语法上非常类似于 Azure 资源管理器模板。 它们是包含项属性名称和值的 JSON 文件，但添加了以下主要概念：

- 参数：允许在正文部分中指定属性值，使用模板时，仅需提供参数值。 例如，池的完整定义应放入正文且仅为 `poolId` 定义一个参数；因此仅需提供一个池 ID 字符串来创建池。 模板正文可由了解 Batch 和 Batch 运行的应用程序的人进行创建；使用模板时，必须提供仅作者定义的参数值。 这样，不具备任何专业批处理和/或应用程序知识的用户便可使用这些模板。
- 变量：允许在一个位置指定简单或复杂参数值，并在模板正文中的一个或多个位置使用它们。 变量可以简化和减小模板大小，以及通过在单个位置更改属性使其更易于维护。
- 高级构造：Batch API 中尚不可用的模板提供了一些更高级别的构造。 例如，可以在作业模板中定义任务工厂，以使用常见任务定义为作业创建多项任务。 这些构造避免了进行编码以动态创建多个 JSON 文件（如每个任务创建一个文件），以及创建脚本文件以通过程序包管理器安装应用程序等需求。

### <a name="pool-templates"></a>池模板

池模板支持参数和变量的标准模板功能。 它们还支持包引用，其中可选择允许使用包管理器将软件复制到池节点。 包管理器和包 ID 是在包引用中指定的。 通过声明一个或多个包，你不再需要创建用来获取所需包的脚本、安装该脚本以及在每个池节点上运行该脚本。

下面是用于创建安装有 ffmpeg 的 Linux VM 池的模板的示例。 若要使用该模板，只需要提供池 ID 字符串和池中的 VM 数：

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "taskSlotsPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

如果模板文件名为 _pool-ffmpeg.json_，请如下所示调用该模板：

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI 会提示你提供 `poolId` 和 `nodeCount` 参数的值。 也可以提供 JSON 文件中的参数。 例如：

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

如果参数 JSON 文件名为 pool-parameters.json，请按以下所示调用该模板：

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>作业模板

作业模板支持参数和变量的标准模板功能。 它们还支持任务工厂构造，此构造通过一个任务定义为作业创建多项任务。 支持三种类型的任务工厂：参数扫描、按文件创建任务和任务集合。

下面是一个模板示例，它创建使用 ffmpeg 将 MP4 视频文件转码为两个较低分辨率之一的作业： 它针对每个源视频文件创建一个任务。 有关作业输入和输出文件组的详细信息，请参阅[文件组和文件传输](#file-groups-and-file-transfer)。

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": {
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

如果模板文件名为 _job-ffmpeg.json_，请如下所示调用该模板：

```azurecli
az batch job create --template job-ffmpeg.json
```

CLI 同样会提示你提供参数的值。 也可以提供 JSON 文件中的参数。

### <a name="use-templates-in-batch-explorer"></a>使用 Batch Explorer 中的模板

可以将 Batch CLI 模板上传到 [Batch Explorer](https://github.com/Azure/BatchExplorer) 桌面应用程序，以创建批处理池或作业。 还可以在 Batch Explorer 库中选择预定义的池和作业模板。

上传模板：

1. 在 Batch Explorer 中，选择“库” > “本地模板” 。
2. 选择或拖放本地池或作业模板。
3. 选择“使用此模板”，并按照屏幕上的提示操作。

## <a name="file-groups-and-file-transfer"></a>文件组和文件传输

大部分作业和任务均需要输入文件并生成输出文件。 通常，输入文件和输出文件从客户端传输到节点，或者从节点传输到客户端。 Azure Batch CLI 扩展提取文件传输，并利用可与每个 Batch 帐户相关联的存储帐户。

文件组等同于在 Azure 存储帐户中创建的容器。 文件组允许包含子文件夹。

Batch CLI 扩展提供了相关的命令，用以将客户端的文件上传到指定文件组，以及将指定文件组的文件下载到客户端。

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

通过池和作业模板，可将存储在文件组中的文件指定为复制到池节点或离开池节点返回到文件组。 例如，在之前指定的作业模板中，为任务工厂指定文件组 ffmpeg-input ，作为复制到节点以供转码的源视频文件的位置。 文件组 ffmpeg-output 是从运行每个任务的节点复制已转码输出文件的位置。

## <a name="summary"></a>摘要

目前仅对 Azure CLI 添加了模板和文件传输支持。 其目的在于，将可以使用 Batch 的受众扩大到无需使用 Batch API 开发代码的用户，例如研究人员和 IT 用户。 了解 Azure、Batch 和 Batch 运行的应用程序的用户无需编码即可创建模板以创建池和作业。 通过模板参数，对 Batch 和应用程序没有深入了解的用户也可使用这些模板。

试用 Azure CLI 的 Batch 扩展，并通过本文的评论区或 [Batch 社区存储库](https://github.com/Azure/Batch)向我们提供任何反馈或建议。

## <a name="next-steps"></a>后续步骤

- 有关安装和使用文档、示例和源代码的详细信息，请参阅 [Azure GitHub 存储库](https://github.com/Azure/azure-batch-cli-extensions)。
- 深入了解如何使用 [Batch Explorer](https://github.com/Azure/BatchExplorer) 来创建和管理 Batch 资源。
