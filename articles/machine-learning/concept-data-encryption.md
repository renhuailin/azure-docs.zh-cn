---
title: 使用 Azure 机器学习进行数据加密
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习计算和数据存储如何提供静态数据加密和传输中数据加密。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/02/2021
ms.openlocfilehash: e367f6685e73cb6cdfd9e777e6cb554c186c616c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429217"
---
# <a name="data-encryption-with-azure-machine-learning"></a>使用 Azure 机器学习进行数据加密

Azure 机器学习在训练模型和执行推理时使用各种 Azure 数据存储服务和计算资源。 上述每种服务和资源在为静态数据和传输中数据提供加密方面都有其自己的方式。 本文介绍每一种服务和资源以及哪种最适合你的方案。

> [!IMPORTANT]
> 对于培训期间的生产级别加密，Microsoft 建议使用 Azure 机器学习计算群集。 对于推断期间的生产级别加密，Microsoft 建议使用 Azure Kubernetes 服务。
>
> Azure 机器学习计算实例是开发/测试环境。 使用它时，我们建议将文件（如笔记本和脚本）存储在文件共享中。 数据应存储在数据存储中。

## <a name="encryption-at-rest"></a>静态加密

> [!IMPORTANT]
> 如果工作区包含敏感数据，我们建议在创建工作区时设置 [hbi_workspace 标志](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)。 只能在创建工作区时设置 `hbi_workspace` 标志。 不能更改现有工作区的这个标志。

`hbi_workspace` 标志控制 [Microsoft 为诊断而收集的数据](#microsoft-collected-data)量，并[在 Microsoft 托管环境中启用其他加密](../security/fundamentals/encryption-atrest.md)。 此外，该标志启用以下操作：

* 开始加密 Azure 机器学习计算群集中的本地暂存磁盘，前提是尚未在该订阅中创建任何以前的群集。 否则，需要提供支持票证来启用对计算群集的暂存磁盘的加密 
* 在不同运行之间清理本地暂存磁盘
* 使用密钥保管库，将存储帐户、容器注册表和 SSH 帐户的凭据从执行层安全地传递到计算群集

当此标志设置为 True 时，一个可能的影响是增加了排查问题的难度。 这可能是因为某些遥测数据未发送给 Microsoft，并且对成功率或问题类型的可见性较低，因此当此标志为 True 时，可能无法主动作出反应。

> [!TIP]
> `hbi_workspace` 标志不会影响传输中的加密，仅影响静态加密。

### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure 机器学习在绑定到 Azure 机器学习工作区和订阅的 Azure Blob 存储帐户中存储快照、输出与日志。 Azure Blob 存储中存储的所有数据已通过 Microsoft 管理的密钥静态加密。

有关如何对 Azure Blob 存储中存储的数据使用自己密钥的信息，请参阅[使用 Azure Key Vault 中客户管理的密钥进行 Azure 存储加密](../storage/common/customer-managed-keys-configure-key-vault.md)。

训练数据通常也存储在 Azure Blob 存储中，因此可供训练计算目标访问。 此存储并不受 Azure 机器学习管理，而是作为远程文件系统装载到计算目标上。

如果需要轮换或撤销密钥，则可以随时执行此操作。 轮换密钥时，存储帐户将开始使用新密钥（最新版本）来加密静态数据。 撤消（禁用）密钥时，存储帐户会处理失败的请求。 轮换或撤消操作通常需要一小时才能生效。

有关重新生成访问密钥的信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 机器学习在 Azure Cosmos DB 实例中存储元数据。 此实例与 Azure 机器学习管理的 Microsoft 订阅相关联。 Azure Cosmos DB 中存储的所有数据都使用 Microsoft 托管密钥进行静态加密。

若要使用自己的（客户管理的）密钥来加密 Azure Cosmos DB 实例，可以创建一个专用的 Cosmos DB 实例用于工作区。 如果要将数据（例如运行历史记录信息）存储在 Microsoft 订阅中托管的多租户 Cosmos DB 实例之外，则建议采用此方法。 

若要使用客户管理的密钥来预配订阅中的 Cosmos DB 实例，请执行以下操作：

* 在订阅中注册 Microsoft.MachineLearning 和 Microsoft.DocumentDB 资源提供程序（如果尚未注册）。

* 创建 Azure 机器学习工作区时，请使用以下参数。 这两个参数都是必需的，并且在 SDK、Azure CLI、REST API 和资源管理器模板中受支持。

    * `resource_cmk_uri`：此参数是密钥保管库中客户管理的密钥的完整资源 URI，其中包括[密钥的版本信息](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此参数是订阅中密钥保管库的资源 ID。 此密钥保管库位于要用于 Azure 机器学习工作区的同一区域和订阅中。 
    
        > [!NOTE]
        > 此密钥保管库实例可能不同于在预配工作区时 Azure 机器学习创建的密钥保管库。 如果要对工作区使用相同的密钥保管库实例，请在使用 [key_vault 参数](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)预配工作区时传递相同的密钥保管库。 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

如果需要轮换或撤销密钥，则可以随时执行此操作。 轮换密钥时，Cosmos DB 将开始使用新密钥（最新版本）来加密静态数据。 撤消（禁用）密钥时，Cosmos DB 会处理失败的请求。 轮换或撤消操作通常需要一小时才能生效。

有关 Cosmos DB 的客户管理的密钥的详细信息，请参阅[为 Azure Cosmos DB 帐户配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)。

### <a name="azure-container-registry"></a>Azure 容器注册表

注册表（Azure 容器注册表）中的所有容器映像均已进行静态加密。 Azure 会在存储映像之前自动将其加密，并在 Azure 机器学习提取映像时将其解密。

若要使用自己的（客户管理的）密钥来加密 Azure 容器注册表，需要创建自己的 ACR 并在预配工作区时附加它，或者加密预配工作区时创建的默认实例。

> [!IMPORTANT]
> Azure 机器学习要求在 Azure 容器注册表中启用管理员帐户。 创建容器注册表时，默认情况下此设置已禁用。 有关如何启用管理员帐户的信息，请参阅[管理员帐户](../container-registry/container-registry-authentication.md#admin-account)。
>
> 为工作区创建 Azure 容器注册表后，请不要将其删除。 删除该注册表将损坏 Azure 机器学习工作区。

有关使用现有 Azure 容器注册表创建工作区的示例，请参阅以下文章：

* [使用 Azure CLI 创建 Azure 机器学习工作区](how-to-manage-workspace-cli.md)
* [使用 Python SDK 创建工作区](how-to-manage-workspace.md?tabs=python#create-a-workspace)。
* [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure 容器实例

可以使用客户管理的密钥来加密已部署的 Azure 容器实例 (ACI) 资源。 用于 ACI 的客户管理的密钥可存储在用于工作区的 Azure Key Vault 中。 有关生成密钥的信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)。

若要在将模型部署到 Azure 容器实例时使用密钥，请使用 `AciWebservice.deploy_configuration()` 创建新的部署配置。 使用以下参数提供密钥信息：

* `cmk_vault_base_url`：包含密钥的密钥保管库的 URL。
* `cmk_key_name`：键的名称。
* `cmk_key_version`：密钥版本。

有关如何创建和使用部署配置的详细信息，请参阅以下文章：

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 参考
* [部署方式和位置](how-to-deploy-and-where.md)
* [将模型部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)

有关如何将客户管理的密钥用于 ACI 的详细信息，请参阅[使用客户管理的密钥加密数据](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

随时可以使用客户管理的密钥来加密已部署的 Azure Kubernetes 服务资源。 有关详细信息，请参阅[在 Azure Kubernetes 服务中使用自己的密钥](../aks/azure-disk-customer-managed-keys.md)。 

此过程允许加密 Kubernetes 群集中已部署的虚拟机的数据和 OS 磁盘。

> [!IMPORTANT]
> 此过程仅适用于 AKS K8s 1.17 或更高版本。 Azure 机器学习在 2020 年 1 月 13 日添加了对 AKS 1.17 的支持。

### <a name="machine-learning-compute"></a>机器学习计算

**计算群集** Azure 存储中存储的每个计算节点的 OS 磁盘，已通过 Azure 机器学习存储帐户中由 Microsoft 管理的密钥进行加密。 此计算目标是暂时的；没有排队的运行时，群集通常会缩减。 底层虚拟机将解除预配，OS 磁盘将被删除。 OS 磁盘不支持 Azure 磁盘加密。 

每个虚拟机还包含一个本地临时磁盘用于 OS 操作。 如果需要，可以使用该磁盘来暂存训练数据。 如果创建了工作区（`hbi_workspace` 参数设置为 `TRUE`），则临时磁盘将加密。 此环境的生存期较短（与运行的持续时间相当），加密支持仅限于系统托管的密钥。

**计算实例** 存储计算实例的 OS 磁盘使用 Azure 机器学习存储帐户中的 Microsoft 托管密钥进行加密。 如果创建了工作区（`hbi_workspace` 参数设置为 `TRUE`），则计算实例上的本地临时磁盘将通过 Microsoft 托管密钥进行加密。 OS 和临时磁盘不支持客户管理的密钥加密。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 可在 Azure 机器学习管道中使用。 默认情况下，Azure Databricks 使用的 Databricks 文件系统 (DBFS) 使用 Microsoft 托管密钥进行加密。 若要将 Azure Databricks 配置为使用客户管理的密钥，请参阅[在默认（根）DBFS 上配置客户管理的密钥](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="microsoft-generated-data"></a>Microsoft 生成的数据

使用自动化机器学习等服务时，Microsoft 可能会生成经过预处理的暂用数据用于训练多个模型。 此数据存储在工作区中的数据存储内，使你可以适当地强制实施访问控制和加密。

你可能还想要加密[从已部署的终结点记录到 Azure Application Insights 实例的诊断信息](how-to-enable-app-insights.md)。

## <a name="encryption-in-transit"></a>传输中加密

Azure 机器学习使用 TLS 来保护各种 Azure 机器学习微服务之间的内部通信。 所有 Azure 存储访问也都通过安全通道进行。

Azure 机器学习使用 TLS 来保护对评分终结点的外部调用。 有关详细信息，请参阅[使用 TLS 通过 Azure 机器学习来保护 Web 服务](./how-to-secure-web-service.md)。

## <a name="data-collection-and-handling"></a>数据收集和处理

### <a name="microsoft-collected-data"></a>Microsoft 收集的数据

Microsoft 可能会收集非用户标识信息，如资源名称（例如数据集名称或机器学习试验名称）或用于诊断的作业环境变量。 所有此类数据都使用 Microsoft 托管密钥存储在 Microsoft 拥有的订阅中托管的存储中，并遵循 [Microsoft 的标准隐私策略和数据处理标准](https://privacy.microsoft.com/privacystatement)。 此数据与工作区保存在同一区域。

Microsoft 还建议不要在环境变量中存储敏感信息（如帐户密钥机密）。 我们会记录、加密和存储环境变量。 同样，为 [run_id](/python/api/azureml-core/azureml.core.run%28class%29) 命名时，请避免包含用户名或机密项目名称等敏感信息。 此信息可能会出现在可供 Microsoft 支持部门工程师访问的遥测日志中。

预配工作区时，可以通过将 `hbi_workspace` 参数设置为 `TRUE` 来选择退出收集诊断数据。 使用 AzureML Python SDK、Azure CLI、REST API 或 Azure 资源管理器模板时支持此功能。

## <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 机器学习使用与工作区关联的 Azure Key Vault 实例来存储各种凭据：

* 关联的存储帐户连接字符串
* Azure 容器存储库实例的密码
* 数据存储的连接字符串

Azure HDInsight 等计算目标和 VM 的 SSH 密码与密钥存储在与 Microsoft 订阅关联的独立 Key Vault 中。 Azure 机器学习不会存储用户提供的任何密码或密钥， 而是生成、授权并存储自身的 SSH 密钥，用于连接到 VM 和 HDInsight 以运行试验。

每个工作区有一个关联的系统分配的托管标识，该标识与工作区同名。 此托管标识可以访问密钥保管库中的所有密钥、机密和证书。

## <a name="next-steps"></a>后续步骤

* [连接到 Azure 存储](how-to-access-data.md)
* [从数据存储中获取数据](how-to-create-register-datasets.md)
* [连接到数据](how-to-connect-data-ui.md)
* [使用数据集进行训练](how-to-train-with-datasets.md)
