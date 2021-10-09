---
title: 使用作业创建 UI 创建训练作业
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习工作室中的作业创建 UI 创建训练作业。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 06/22/2021
ms.reviewer: laobri
ms.openlocfilehash: 94eb2beb95a43e422e8bff6c3ab53f7d2e6c208b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094702"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>使用作业创建 UI（预览版）创建训练作业

可通过多种方式使用 Azure 机器学习来创建训练作业。 可以使用 CLI（参阅[使用 CLI (v2)（预览版）训练模型（创建作业）](how-to-train-cli.md)）、REST API（参阅[使用 REST 训练模型（预览版）](how-to-train-with-rest.md)），或者可以使用 UI 直接创建训练作业。 本文介绍如何使用你自己的数据和代码，通过 Azure 机器学习工作室中的作业创建 UI 训练机器学习模型。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* Azure 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。 

* 了解 Azure 机器学习中的作业是什么。 参阅[作业简介](how-to-train-cli.md#introducing-jobs)。

## <a name="get-started"></a>入门

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。 

1. 选择订阅和工作区。
 
* 可以从主页进入作业创建 UI。 单击“新建”并选择“作业” 。 
[![Azure 机器学习工作室主页](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* 或者，可以通过左侧窗格进入作业创建 UI。 单击“+新建”并选择“作业” 。 
[![Azure 机器学习工作室左侧导航栏](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* 或者，如果你在“试验”页中操作，可以转到“所有运行”选项卡并单击“创建作业” 。 
[![“试验”页上的作业创建 UI 入口](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

这些选项会将你转到作业创建面板，其中提供了一个用于配置和创建训练作业的向导。 

## <a name="select-compute-resources"></a>选择计算资源

作业创建 UI 中的第一个步骤是选择要对其运行作业的计算目标。 作业创建 UI 支持多种计算类型：

| 计算类型 | 简介 | 
| --- | --- | 
| 计算实例 | [什么是 Azure 机器学习计算实例？](concept-compute-instance.md) | 
| 计算群集 | [什么是计算群集？](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| 附加的 Kubernetes 群集 | [配置已启用 Azure Arc 的机器学习（预览版）](how-to-attach-arc-kubernetes.md)。 | 

1. 选择计算类型
1. 选择现有的计算资源。 下拉列表显示了节点信息和 SKU 类型以帮助你进行选择。
1. 对于计算群集或 Kubernetes 群集，还可以在“实例计数”中指定你希望用于作业的节点数。 默认实例数为 1。 
1. 如果对所做的选择满意，请选择“下一步”。 
 [![选择计算群集](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

如果你是第一次使用 Azure 机器学习，将会看到一个空列表以及一个用于创建新计算的链接。 

 [![新建计算实例](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

有关创建各种类型的计算的详细信息，请参阅：

| 计算类型 | 如何 | 
| --- | --- | 
| 计算实例 | [创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md) | 
| 计算群集 | [创建 Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md) | 
| 附加的 Kubernetes 群集 | [附加已启用 Azure Arc 的 Kubernetes 群集](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>指定所需的环境

选择计算目标后，需要指定作业的运行时环境。 作业创建 UI 支持三种类型的环境：

* 特选环境
* 自定义环境
* 容器注册表映像 

### <a name="curated-environments"></a>特选环境

特选环境是 Azure 定义的、在常见 ML 工作负载中使用的 Python 包集合。 默认情况下，特选环境在工作区中可用。 这些环境由缓存的 Docker 映像支持，降低了运行准备开销。 “特选环境”页中的卡显示了每个环境的详细信息。 有关详细信息，请参阅 [Azure 机器学习中的特选环境](resource-curated-environments.md)。

 [![特选环境](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>自定义环境

自定义环境是你自己指定的环境。 可以指定一个环境，也可以重复使用已创建的环境。 有关详细信息，请参阅[在 Azure 机器学习工作室中管理软件环境（预览版）](how-to-manage-environments-in-studio.md#create-an-environment)。 

### <a name="container-registry-image"></a>容器注册表映像

如果你不想要使用 Azure 机器学习特选环境或指定自己的自定义环境，可以使用公共容器注册表（例如 [Docker Hub](https://hub.docker.com/)）中的 Docker 映像。 如果该映像位于专用容器中，请将“这是专用容器注册表”开关切换到“开”。 对于专用注册表，需要输入有效的用户名和密码，使 Azure 能够获取该映像。 
[![容器注册表映像](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>配置作业

指定环境后，可以使用其他设置配置作业。 

|字段| 说明|
|------| ------|
|作业名称| 作业名称字段用于唯一标识作业。 它还用作作业的显示名称。 此字段是可选的；如果你未输入任何内容，Azure 将为作业生成 GUID 名称。 注意：作业名称必须唯一。|
|实验名称| 这有助于在 Azure 机器学习工作室中组织作业。 每个作业的运行记录将组织到工作室“试验”选项卡中的相应试验下。默认情况下，Azure 会将作业放在“默认”试验中。|
|代码| 可以从计算机上传代码文件或文件夹，或者从工作区的默认 Blob 存储上传代码文件。 在你做出选择后，Azure 将显示要上传的文件。 |
|命令| 要执行的命令。 可以将命令行参数显式写入到命令中，或者使用大括号表示法从其他部分（具体而言，是“输入”，具体请参阅下一部分）推断这些参数。|
|输入| 指定输入绑定。 支持三种类型的输入：1) Azure 机器学习注册的数据集；2) 工作区默认 Blob 存储；3) 上传本地文件。 可以添加多个输入。 |
|环境变量| 通过设置环境变量可以提供作业的动态配置。 可在此处添加变量和值。|
|标记| 将标记添加到作业以帮助进行组织。|

### <a name="specify-code-and-inputs-in-the-command-box"></a>在命令框中指定代码和输入

#### <a name="code"></a>代码

命令将从上传的代码文件夹的根目录运行。 选择代码文件或文件夹后，可以看到要上传的文件。 将相对路径复制到包含入口点的代码，并将其粘贴到标有“输入命令以启动作业”的框中。 

如果代码位于根目录中，则可以直接在命令中引用它。 例如：`python main.py`。

如果代码不在根目录中，则应使用相对路径。 例如，[字词语言模型](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model)的结构为：

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
此处，源代码位于 `src` 子目录中。 命令将是 `python ./src/main.py`（加上其他命令行参数）。

[![在命令中引用代码](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>输入

可通过两种方式执行输入绑定： 

* 输入名称：在命令中使用输入时，需要指定输入名称。 若要指示输入变量，请使用 `{inputs.input_name}` 格式。 例如：`{inputs.wiki}`。 然后，可以在命令中引用该输入，例如 `--data {inputs.wiki}`。
[![在命令中引用输入名称](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* 路径：可以使用 `--data .path` 指定云位置。 路径是在“计算上的路径”字段中输入的内容。
[![在命令中引用输入路径](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>在用于启动作业的命令中，必须在“计算上的路径”值前面添加一个句点 。 例如，`/data/wikitext-2` 变为 `./data/wikitext-2`。

## <a name="review-and-create"></a>查看并创建 

配置作业后，选择“下一步”转到“查看”页 。 若要修改设置，请选择铅笔图标并进行更改。 

可以选择“查看 YAML 规范”，以查看和下载此作业配置生成的 YAML 文件。 此作业 yaml 文件可用于从 CLI (v2) 提交作业。 （参阅[使用 CLI (v2)（预览版）训练模型（创建作业）](how-to-train-cli.md)。）[![查看 yaml 规范](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![Yaml 规范](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

若要启动作业，请选择“创建”。 创建作业后，Azure 将显示运行详细信息页，可在其中监视和管理训练作业。 

## <a name="next-steps"></a>后续步骤

* [使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)。

* [使用 CLI (v2)（预览版）训练模型（创建作业）](how-to-train-cli.md)
