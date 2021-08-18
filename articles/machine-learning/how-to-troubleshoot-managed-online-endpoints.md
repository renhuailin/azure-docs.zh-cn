---
title: 排查托管联机终结点部署问题（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何排查托管联机终结点出现的一些常见部署和评分错误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 05/13/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 9f50fd0ff60f819717dbcb1e3878b4caf56c563b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729785"
---
# <a name="troubleshooting-managed-online-endpoints-deployment-and-scoring-preview"></a>排查托管联机终结点的部署和评分问题（预览版）

了解如何解决 Azure 机器学习托管联机终结点（预览版）的常见部署和评分问题。

本文档采用一种可方便你着手进行故障排除的结构：

1. 在云中部署之前，请使用[本地部署](#deploy-locally)在本地测试和调试模型。
1. 使用[容器日志](#get-container-logs)帮助调试问题。
1. 了解可能会出现的[常见部署错误](#common-deployment-errors)及其解决方法。

[HTTP 状态代码](#http-status-codes)部分解释了在使用 REST 请求为终结点评分时，调用和预测错误如何映射到 HTTP 状态代码。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [安装、设置和使用 2.0 CLI（预览版）](how-to-configure-cli.md)。

## <a name="deploy-locally"></a>本地部署

本地部署会将模型部署到本地 Docker 环境。 本地部署可用于在部署到云之前进行测试和调试。

本地部署支持创建、更新和删除本地终结点。 它还允许从终结点调用和获取日志。 若要使用本地部署，请将 `--local` 添加到相应的 CLI 命令：

```azurecli
az ml endpoint create -n <endpoint-name> -f <spec_file.yaml> --local
```
在本地部署过程中，将执行以下步骤：

- Docker 生成新的容器映像，或者从本地 Docker 缓存拉取现有映像。 如果某个现有映像与规范文件的环境部分相匹配，将使用该映像。
- Docker 启动装载了本地项目（如模型和代码文件）的新容器。

有关详细信息，请参阅[使用托管联机终结点（预览版）在本地部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)。

## <a name="get-container-logs"></a>获取容器日志

无法直接访问部署了模型的 VM。 但是，可以从该 VM 上运行的某些容器获取日志。 信息量取决于部署的预配状态。 如果指定的容器已启动且正在运行，则你会看到其控制台输出，否则会收到一条请你稍后重试的消息。

若要查看容器的日志输出，请使用以下 CLI 命令：

```azurecli
az ml endpoint get-logs -n <endpoint-name> -d <deployment-name> -l 100
```

或

```azurecli
    az ml endpoint get-logs --name <endpoint-name> --deployment <deployment-name> --lines 100
```

如果尚未通过 `az configure` 设置 `--resource-group` 和 `--workspace-name` 参数，请将这些参数添加到上述命令。

若要查看有关如何设置这些参数的信息并确定是否已设置当前值，请运行：

```azurecli
az ml endpoint get-logs -h
```

默认情况下，日志是从推理服务器拉取的。 日志包括来自推理服务器的控制台日志，其中包含“score.py”代码中的 print/log 语句。

> [!NOTE]
> 如果使用 Python 日志记录，请确保为要发布到日志中的消息使用正确的日志记录级别顺序。 例如 INFO。


还可以通过传递 `–-container storage-initializer` 从存储初始化表达式容器获取日志。 这些日志包含有关代码和模型数据是否已成功下载到容器的信息。

添加 `--help` 和/或 `--debug` 命令以查看更多信息。 包含 `x-ms-client-request-id` 头以帮助进行故障排除。

## <a name="common-deployment-errors"></a>常见部署错误

下面是作为部署操作状态的一部分报告的常见部署错误列表。

### <a name="err_1100-not-enough-quota"></a>ERR_1100: 没有足够的配额

在部署模型之前，需有足够的计算配额。 此配额定义每个订阅、每个工作区、每个 SKU 和每个区域可用的虚拟核心数。 每次部署都会扣减可用配额，删除部署后，会根据 SKU 的类型重新增加配额。

一种可能的缓解措施是检查能否删除未使用的部署。 或者，可以提交[配额提高请求](./how-to-manage-quotas.md)。

### <a name="err_1101-out-of-capacity"></a>ERR_1101：容量不足

由于 Azure 机器学习容量不足，无法预配指定的 VM 大小。 请稍后重试，或尝试部署到其他区域。

### <a name="err_1200-unable-to-download-user-container-image"></a>ERR_1200: 无法下载用户容器映像

在预配计算后创建部署期间，Azure 会尝试从工作区专用的 Azure 容器注册表 (ACR) 中拉取用户容器映像。 可能会出现两种问题。

- 找不到用户容器映像。

  请确保该容器映像在工作区 ACR 中可用。
例如，如果映像为 `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest`，请使用 `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table` 检查存储库。

- 访问 ACR 时出现权限问题。

  为了拉取映像，Azure 将使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)访问 ACR。 

  - 如果使用 SystemAssigned 创建了关联的终结点，则系统会自动授予 Azure 基于角色的访问控制 (RBAC) 权限，此时不需要任何进一步的权限。
  - 如果使用 UserAssigned 创建了关联的终结点，则用户的托管标识必须对工作区 ACR 拥有 AcrPull 权限。

若要获取有关此错误的更多详细信息，请运行：

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --tail 100
```

### <a name="err_1300-unable-to-download-user-modelcode-artifacts"></a>ERR_1300: 无法下载用户模型\代码项目

在预配计算资源后创建部署期间，Azure 会尝试将用户模型和代码项目装载到工作区存储帐户中的用户容器。

- 找不到用户模型\代码项目。

  - 确保将模型和代码项目注册到部署所在的同一工作区。 使用 `show` 命令显示工作区中模型或代码项目的详细信息。 例如： 
  
    ```azurecli
    az ml model show --name <model-name>
    az ml code show --name <code-name> --version <version>
    ```

  - 还可以检查 Blob 是否存在于工作区存储帐户中。

    例如，如果 Blob 为 `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl`，则可使用以下命令检查它是否存在：`az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

- 访问 ACR 时出现权限问题。

  为了拉取 Blob，Azure 将使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)访问存储帐户。

  - 如果使用 SystemAssigned 创建了关联的终结点，则系统会自动授予 Azure 基于角色的访问控制 (RBAC) 权限，此时不需要任何进一步的权限。

  - 如果使用 UserAssigned 创建了关联的终结点，则用户的托管标识必须对工作区存储帐户拥有“存储 Blob 数据读取者”权限。

若要获取有关此错误的更多详细信息，请运行：

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --lines 100
```

### <a name="err_1350-unable-to-download-user-model-not-enough-space-on-the-disk"></a>ERR_1350：无法下载用户模型，磁盘空间不足

如果模型的大小大于可用磁盘空间，则会发生此问题。 试用具有更多磁盘空间的 SKU。

### <a name="err_2100-unable-to-start-user-container"></a>ERR_2100: 无法启动用户容器

为了运行在部署过程中提供的 `score.py`，Azure 将创建一个包含 `score.py` 所需全部资源的容器，并在该容器上运行评分脚本。

此错误表示此容器无法启动，也意味着无法评分。 可能是容器请求的资源数量超过了 `instance_type` 可以支持的数量。 如果是这样，请考虑更新联机部署的 `instance_type`。

若要获取确切的错误原因，请运行： 

```azurecli
az ml endpoint get-logs
```

### <a name="err_2200-user-container-has-crashedterminated"></a>ERR_2200: 用户容器已崩溃\已终止

为了运行在部署过程中提供的 `score.py`，Azure 将创建一个包含 `score.py` 所需全部资源的容器，并在该容器上运行评分脚本。  在这种场景下发生的错误表现为此容器在运行时崩溃，这意味着无法评分。 出现以下情况时会发生此错误：

- `score.py` 中有错误。 使用 `get--logs` 来帮助诊断常见问题：
    - 包已导入，但不在 conda 环境中
    - 语法错误
    - `init()` 方法失败
- 未正确设置就绪状态或运行情况探测。
- 容器的环境设置有错误，例如缺少依赖项。

### <a name="err_5000-internal-error"></a>ERR_5000: 内部错误

尽管我们会尽最大努力提供稳定可靠的服务，但有时事情不会按计划进行。 如果收到此错误，表示我们这一端出现了问题，我们需要予以修复。 请提交[客户支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)并随附所有相关信息，我们将解决问题。  

## <a name="http-status-codes"></a>HTTP 状态代码

使用 REST 请求访问托管联机终结点时，返回的状态代码将遵守 [HTTP 状态代码](https://aka.ms/http-status-codes)标准。 下面是有关托管终结点调用和预测错误如何映射到 HTTP 状态代码的详细信息。

| 状态代码| 原因短语 |  返回此代码的可能原因 |
| --- | --- | --- |
| 200 | 确定 | 已在延迟范围内成功执行了模型。 |
| 401 | 未授权 | 你无权执行请求的操作（如评分），或者令牌已过期。 |
| 404 | 未找到 | URL 不正确。 |
| 408 | 请求超时 | 模型执行所用的时间超过了在模型部署配置的 `request_settings` 下的 `request_timeout_ms` 中提供的超时。|
| 413 | 有效负载太大 | 请求有效负载大于 1.5 MB。 |
| 424 | 模型错误；original-code=`<original code>` | 如果模型容器返回非 200 响应，则 Azure 将返回 424。 |
| 424 | 响应有效负载太大 | 如果容器返回大于 1.5 MB 的有效负载，则 Azure 将返回 424。 |
| 429 | 速率限制 | 每秒尝试向终结点发送的请求数超过 100 个。 |
| 429 | 挂起的请求太多 | 模型收到的请求数超过了它可以处理的数量。 在任意时间，我们允许 2 *`max_concurrent_requests_per_instance`* `instance_count` 个请求。 更多的请求将被拒绝。 可以在模型部署配置中的 `request_settings` 和 `scale_settings` 下确认这些设置。 如果使用自动缩放，则模型接收请求的速度比系统纵向扩展的速度更快。 使用自动缩放时，可以尝试通过[指数退避](https://aka.ms/exponential-backoff)来重新发送请求。 这样做可以让系统有时间做出调整。 |
| 500 | 内部服务器错误 | Azure ML 预配的基础结构发生故障。 |

## <a name="next-steps"></a>后续步骤

- [使用托管联机终结点（预览版）部署机器学习模型并为其评分](how-to-deploy-managed-online-endpoints.md)
- [联机终结点的安全推出（预览版）](how-to-safely-rollout-managed-endpoints.md)
- [托管联机终结点（预览版）YAML 参考](reference-yaml-endpoint-managed-online.md)

