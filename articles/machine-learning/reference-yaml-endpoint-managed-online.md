---
title: 托管联机终结点（预览版）YAML 参考
titleSuffix: Azure Machine Learning
description: 了解作为托管联机终结点用于部署模型的 YAML 文件
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: ebb4db34c802c1cd82e0065579cc97ffeb3fe642
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778140"
---
# <a name="cli-v2-managed-online-endpoint-yaml-schema"></a>CLI (v2) 托管联机终结点 YAML 架构

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> 可以[参考](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)一个为托管联机终结点完全指定的示例 YAML

## <a name="schema"></a>架构

可在 https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json 中找到源 JSON 架构。 为方便起见，下面以 JSON 和 YAML 格式提供了架构。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/managedOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/managedOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>备注

| 键 | 说明 |
| --- | --- |
| $schema    | \[可选\] YAML 架构。 可以在浏览器中查看上述示例中的架构，以查看 YAML 文件的所有可用选项。|
| name       | 终结点的名称。 在 Azure 区域级别需是唯一的。|
| traffic | 从终结点转移到每个部署的流量百分比。 流量值的总和需是 100。 |
| auth_mode | 使用 `key` 可执行基于密钥的身份验证，使用 `aml_token` 可执行基于 Azure 机器学习令牌的身份验证。 `key` 不会过期，但 `aml_token` 会过期。 使用 `az ml endpoint list-keys` 命令获取最新的令牌。 |
| 标识 | 用于配置系统分配和用户分配的托管标识。 |
| app_insights_enabled | 启用与 Azure 机器学习工作区关联的 Azure AppInsights 的集成时为 `True`。 默认值为 `False`。
| 标记 | 要与终结点关联的 Azure 标记的字典。 |
| description | 终结点的说明。 |
| 目标 | 如果未定义此键，则终结点将部署为托管联机终结点。 若要使用 AKS，需将此键的值设置为已注册的计算目标的名称，例如 `target:azureml:my-aks`。 
| deployments | 包含要在终结点中创建的部署的列表。 在本例中，只有一个名为 `blue` 的部署。 |

### <a name="attributes-of-the-deployments-key"></a>`deployments` 键的属性
 
| 键 | 说明 |
| --- | --- |
| name  | 部署的名称。 |
| 模型 | 已注册的模型版本的名称，格式为 `model: azureml:my-model:1`。 你还可以指定内联模型属性：`name`、`version` 和 `local_path`。 模型文件将自动上传和注册。 内联规范的缺点是，如果要更新模型文件，则必须手动递增版本。|
| code_configuration.code.local_path | 包含用于为模型评分的所有 Python 源代码的目录。 支持嵌套的目录/包。 |
| code_configuration.scoring_script | 上述评分目录中的 Python 文件。 此 Python 代码必须具有 `init()` 函数和 `run()` 函数。 在创建或更新模型后将调用 `init()` 函数（可以使用该函数在内存中缓存模型，等等）。 每次调用终结点时，都将调用 `run()` 函数来执行实际评分/预测。 |
| 环境 | 包含用于托管模型和代码的 Azure 机器学习环境的详细信息。 适用于生产环境的最佳做法是单独注册模型和环境，并在 YAML 中指定已注册的名称和版本。 例如 `environment: azureml:my-env:1`。 |
| instance_type | 用于承载部署实例的 VM SKU。 有关详细信息，请参阅[托管联机终结点支持的 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)。|
| scale_settings.scale_type | 目前，此值必须是 `manual`。 若要在创建终结点和部署后进行纵向扩展或缩减，请更新 YAML 中的 `instance_count` 并运行命令 `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`。 |
| scale_settings.instance_count | 部署中的实例数。 请根据预期的工作负载确定值。 为实现高可用性，Microsoft 建议将此值至少设置为 `3`。 |
| scale_settings.min_instances | 始终存在的最小实例数。 |
| scale_settings.max_instances | 部署可以扩展到的最大实例数。 将为 max_instances 保留配额。 |
| request_settings.request_timeout_ms | 以毫秒为单位的评分超时。 对于托管联机终结点，默认值为 5000。 |
| request_settings.max_concurrent_requests_per_instance | 每个部署允许的每个节点的最大并发请求数。 默认值为 1。 除非获得 Microsoft 技术支持人员或 Azure 机器学习团队成员的指导，否则不要更改此设置的默认值 1。 |
| request_settings.max_queue_wait_ms | 请求将在队列中停留的最长时间（以毫秒为单位）。 默认为 500。 |
| liveness_probe | 运行情况探测会定期监视容器的运行状况。 |
| liveness_probe.period | 执行运行情况探测的频率（秒）。 默认值为 10 秒。 最小值为 1。 |
| liveness_probe.initial_delay | 启动容器后，启动运行情况探测前的秒数。 默认值为 10。 |
| liveness_probe.timeout | 运行情况探测超时前等待的秒数。默认值为 2 秒。 最小值为 1。 |
| liveness_probe.failure_threshold | 系统将在放弃之前尝试 failure_threshold 次。 默认值为 30。 最小值为 1。 |
| liveness_probe.success_threshold | 失败后，运行情况探测被视为成功的最小连续成功次数。 默认值为 1。 最小值为 1。 |
| readiness_probe | 就绪情况探测会验证容器是否已准备好为流量提供服务。 属性和默认值与运行情况探测相同。 |
| 标记 | 要与部署关联的 Azure 标记的字典。 |
| description | 部署的说明。 |

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
- 了解如何[使用托管联机终结点部署模型](how-to-deploy-managed-online-endpoints.md)
- [排查托管联机终结点的部署和评分问题（预览版）](how-to-troubleshoot-managed-online-endpoints.md)
