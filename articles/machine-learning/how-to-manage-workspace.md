---
title: 在门户或 Python SDK 中管理工作区
titleSuffix: Azure Machine Learning
description: 了解如何通过 Azure 门户或 SDK for Python 管理 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 04/22/2021
ms.topic: how-to
ms.custom: fasttrack-edit, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 2d0d0f70f5a610e77169685a716282bcc0456cab
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729823"
---
# <a name="manage-azure-machine-learning-workspaces-in-the-portal-or-with-the-python-sdk"></a>在门户中或者使用 Python SDK 来管理 Azure 机器学习工作区

在本文中，你将使用 Azure 门户或 [SDK for Python](/python/api/overview/azure/ml/) 为 [Azure 机器学习](overview-what-is-azure-machine-learning.md)创建、查看和删除 [Azure 机器学习工作区](concept-workspace.md)

随着需求的变化或自动化要求的增加，还可使用 [CLI](reference-azure-machine-learning-cli.md) 或通过 [VS Code 扩展](how-to-setup-vs-code.md)来管理工作区。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
* 如果使用 Python SDK，请[安装 SDK](/python/api/overview/azure/ml/install)。

## <a name="limitations"></a>限制

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

默认情况下，创建工作区时还会创建 Azure 容器注册表 (ACR)。  由于 ACR 当前不支持在资源组名称中使用 unicode 字符，因此请使用不包含这些字符的资源组。

## <a name="create-a-workspace"></a>创建工作区

# <a name="python"></a>[Python](#tab/python)

* **默认规范。** 默认情况下，系统会自动创建依赖资源和资源组。 以下代码创建一个名为 `myworkspace` 的工作区，并在 `eastus2` 中创建一个名为 `myresourcegroup` 的资源组。
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    如果需要将一个现有的 Azure 资源组用于该工作区，请将 `create_resource_group` 设置为 False。

* <a name="create-multi-tenant"></a>多个租户。  如果有多个帐户，请添加要使用的 Azure Active Directory 的租户 ID。  在 [Azure 门户](https://portal.azure.com)的“Azure Active Directory 外部标识”下找到你的租户 ID。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[主权云](reference-machine-learning-cloud-parity.md)** 。 如果你在主权云中操作，则需要使用额外的代码向 Azure 进行身份验证。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* 使用现有的 Azure 资源。  你还可以创建一个工作区，该工作区使用带有 Azure 资源 ID 格式的现有 Azure 资源。 请在 Azure 门户中或通过 SDK 查找特定的 Azure 资源 ID。 此示例假设资源组、存储帐户、密钥保管库、App Insights 和容器注册表已存在。

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

有关详细信息，请查看[工作区 SDK 参考](/python/api/azureml-core/azureml.core.workspace.workspace)。

如果在访问订阅时遇到问题，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)以及 [Azure 机器学习中的身份验证](https://aka.ms/aml-notebook-auth)笔记本。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 使用 Azure 订阅的凭据登录到 [Azure 门户](https://portal.azure.com/)。 

1. 在 Azure 门户的左上角，选择“+ 创建资源”。

      ![创建新资源](./media/how-to-manage-workspace/create-workspace.gif)

1. 使用搜索栏查找“机器学习”。

1. 选择“机器学习”。

1. 在“机器学习”窗格中，选择“创建”以开始 。

1. 提供以下信息来配置新工作区：

   字段|说明 
   ---|---
   工作区名称 |输入用于标识工作区的唯一名称。 本示例使用 docs-ws。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。 工作区名称不区分大小写。
   订阅 |选择要使用的 Azure 订阅。
   资源组 | 使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 本示例使用 docs-aml。 需要“参与者”或“所有者”角色才能使用现有资源组。  有关访问权限的详细信息，请参阅[管理对 Azure 机器学习工作区的访问权限](how-to-assign-roles.md)。
   区域 | 选择离你的用户和数据资源最近的 Azure 区域来创建工作区。
   | 存储帐户 | 工作区的默认存储帐户。 默认情况下，会创建一个新的存储帐户。 |
   | 密钥保管库 | 工作区使用的 Azure Key Vault。 默认情况下，会创建一个新的存储帐户。 |
   | Application Insights | 工作区的 Application Insights 实例。 默认情况下，会创建一个新的存储帐户。 |
   | 容器注册表 | 工作区的 Azure 容器注册表。 默认情况下，一开始不会为工作区创建新的 Azure 容器注册表， 而是在训练或部署过程中当你创建 Docker 映像时根据需要创建。 |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="配置工作区。":::

1. 完成工作区配置后，选择“查看 + 创建”。 （可选）使用[网络](#networking)和[高级](#advanced)部分为工作区配置更多设置。

1. 查看设置并进行任何其他更改或更正。 如果对设置感到满意，请选择“创建”。

   > [!Warning] 
   > 在云中创建工作区可能需要几分钟时间。

   完成创建后，会显示部署成功消息。 
 
 1. 若要查看新工作区，请选择“转到资源”。
 
---



### <a name="networking"></a>网络  

> [!IMPORTANT]  
> 有关将专用终结点和虚拟网络与工作区结合使用的详细信息，请参阅[网络隔离和隐私](how-to-network-security-overview.md)。


# <a name="python"></a>[Python](#tab/python)

Azure 机器学习 Python SDK 提供 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) 类，此类可与 [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 配合使用来创建具有专用终结点的工作区。 此类需要现有虚拟网络。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 默认的网络配置是使用可在公共 Internet 上访问的公共终结点。 若要将对工作区的访问权限限制到已创建的 Azure 虚拟网络，可以改为选择“专用终结点”（预览版）作为“连接方法”，然后使用“+ 添加”来配置终结点。   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="选择专用终结点":::  

1. 在“创建专用终结点”表单上，设置要使用的位置、名称和虚拟网络。 如果要将此终结点与某个专用 DNS 区域配合使用，请选择“与专用 DNS 区域集成”，然后使用“专用 DNS 区域”字段选择该区域。 选择“确定”以创建终结点。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="创建专用终结点":::   

1. 完成网络配置后，可以选择“查看 + 创建”，或转到可选的“高级”配置。

---

> [!IMPORTANT]  
> 将专用终结点与 Azure 机器学习工作区配合使用的功能目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。     
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="vulnerability-scanning"></a>漏洞扫描

Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 你应该允许 Azure 安全中心扫描你的资源并遵循其建议。 有关详细信息，请参阅[通过安全中心扫描 Azure 容器注册表映像](../security-center/defender-for-container-registries-introduction.md)和 [Azure Kubernetes 服务与安全中心的集成](../security-center/defender-for-kubernetes-introduction.md)。

### <a name="advanced"></a>高级

默认情况下，工作区的元数据存储在 Microsoft 维护的 Azure Cosmos DB 实例中。 该数据是使用 Microsoft 管理的密钥加密的。

若要限制 Microsoft 在你的工作区中收集的数据，请在门户中选择“高业务影响工作区”，或在 Python 中设置 `hbi_workspace=true `。 有关此设置的详细信息，请参阅[静态加密](concept-data-encryption.md#encryption-at-rest)。

> [!IMPORTANT]  
> 只能在创建工作区时选择高业务影响。 在创建工作区后，不能更改此设置。   

#### <a name="use-your-own-key"></a>使用自己的密钥

你可以提供自己的密钥来加密数据。 这样做会创建在 Azure 订阅中存储元数据的 Azure Cosmos DB 实例。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

通过以下步骤提供你自己的密钥：

> [!IMPORTANT]  
> 在执行这些步骤之前，必须先执行以下操作：   
>
> 1. 授予机器学习应用（在“标识和访问管理”中）对订阅的参与者权限。  
> 1. 按照[配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)中的步骤完成以下操作：
>     * 注册 Azure Cosmos DB 提供程序
>     * 创建和配置 Azure Key Vault
>     * 生成密钥
>   
>     你无需手动创建 Azure Cosmos DB 实例，系统会在创建工作区期间为你创建一个实例。 将使用一个基于 `<your-workspace-resource-name>_<GUID>` 模式的名称在单独的资源组中创建此 Azure Cosmos DB 实例。   
>   
> 在创建工作区后，不能更改此设置。 如果删除工作区使用的 Azure Cosmos DB，则还必须删除正在使用它的工作区。

# <a name="python"></a>[Python](#tab/python)

使用 `cmk_keyvault` 和 `resource_cmk_uri` 指定客户管理的密钥。

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 选择“客户管理的密钥”，然后选择“单击以选择密钥”。

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="客户管理的密钥":::

1. 在“从 Azure Key Vault 选择密钥”表单中选择现有的 Azure Key Vault、一个它所包含的密钥以及密钥版本。 此密钥用于加密 Azure Cosmos DB 中存储的数据。 最后使用“选择”按钮来使用此密钥。

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="选择密钥":::

---

### <a name="download-a-configuration-file"></a>下载配置文件

如果要创建[计算实例](quickstart-create-resources.md)，请跳过此步骤。  该计算实例已为你创建此文件的副本。

# <a name="python"></a>[Python](#tab/python)

如果你计划在本地环境使用引用此工作区 (`ws`) 的代码，请编写配置文件：

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

如果计划使用引用此工作区的本地环境中的代码，请从工作区的“概述”部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)

---

使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建计算实例时，此文件会添加到 VM 上的正确目录中。

## <a name="connect-to-a-workspace"></a>连接到工作区

在 Python 代码中，创建一个用于连接到工作区的工作区对象。  此代码将读取配置文件的内容以查找工作区。  如果你尚未进行身份验证，系统会提示你登录。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>多个租户。  如果有多个帐户，请添加要使用的 Azure Active Directory 的租户 ID。  在 [Azure 门户](https://portal.azure.com)的“Azure Active Directory 外部标识”下找到你的租户 ID。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[主权云](reference-machine-learning-cloud-parity.md)** 。 如果你在主权云中操作，则需要使用额外的代码向 Azure 进行身份验证。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
如果在访问订阅时遇到问题，请参阅[为 Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)以及 [Azure 机器学习中的身份验证](https://aka.ms/aml-notebook-auth)笔记本。

## <a name="find-a-workspace"></a><a name="view"></a>查找工作区

查看你可以使用的所有工作区的列表。

# <a name="python"></a>[Python](#tab/python)

在 [Azure 门户的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中查找你的订阅。  复制 ID 并在下面的代码中使用它，以便查看可用于该订阅的所有工作区。

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在顶部搜索字段中，键入“机器学习”。  

1. 选择“机器学习”。

   ![搜索 Azure 机器学习工作区](./media/how-to-manage-workspace/find-workspaces.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择工作区即可显示其属性。

---


## <a name="delete-a-workspace"></a>创建工作区

不再需要工作区时，请删除它。  

# <a name="python"></a>[Python](#tab/python)

删除工作区 `ws`：

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

默认操作是不删除与工作区关联的资源，即容器注册表、存储帐户、密钥保管库和应用程序见解。  如果将 `delete_dependent_resources` 设置为 True，则也会删除这些资源。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com/)中，选择要删除的工作区顶部的“删除”。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="删除工作区":::

---

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>故障排除

* Azure 机器学习工作室支持的浏览器：建议使用与操作系统兼容的最新浏览器。 支持以下浏览器：
  * Microsoft Edge（新的 Microsoft Edge（最新版）， 不是旧版 Microsoft Edge）
  * Safari（最新版本，仅限 Mac）
  * Chrome（最新版本）
  * Firefox（最新版本）

* **Azure 门户**： 
  * 如果通过 SDK 的共享链接或 Azure 门户直接访问工作区，则无法查看扩展中包含订阅信息的标准“概述”页面。 此情况下，也无法切换到其他工作区。 若要查看其他工作区，请直接转到 [Azure 机器学习工作室](https://ml.azure.com)并搜索工作区名称。
  * 所有资产（数据集、试验、计算等）仅适用于 [Azure 机器学习工作室](https://ml.azure.com)， 它们不可在 Azure 门户中使用。

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>示例

创建工作区的示例：
* 使用 Azure 门户[创建工作区和计算实例](quickstart-create-resources.md)

## <a name="next-steps"></a>后续步骤

有了工作区后，请了解如何[训练和部署模型](tutorial-train-models-with-aml.md)。

若要详细了解如何根据组织要求规划工作区，请参阅[组织和设置 Azure 机器学习](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization)。