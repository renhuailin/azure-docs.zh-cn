---
title: 在门户中创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 门户或 SDK for Python 中创建、查看和删除 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 4425fdf488665ad555c73c59682041cb23a9ca66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447331"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>创建和管理 Azure 机器学习工作区 

在本文中，你将使用 Azure 门户或 [适用于 Python 的 SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)创建、查看和删除 [Azure 机器学习](overview-what-is-azure-ml.md) [**Azure 机器学习工作区**](concept-workspace.md)

随着你的需求更改或自动化的要求增加，你还可以 [使用 CLI](reference-azure-machine-learning-cli.md)或 [VS Code 扩展](tutorial-setup-vscode-extension.md)创建和删除工作区。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* 如果使用 Python SDK，请 [安装 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。

## <a name="limitations"></a>限制

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="create-a-workspace"></a>创建工作区

# <a name="python"></a>[Python](#tab/python)

* **默认规范。** 默认情况下，将自动创建从属资源以及资源组。 此代码将创建一个名为的工作区 `myworkspace` 和一个名为的资源组 `myresourcegroup` `eastus2` 。
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    `create_resource_group`如果你有要用于工作区的现有 Azure 资源组，则设置为 False。

* <a name="create-multi-tenant"></a>**多个租户。**  如果有多个帐户，请添加要使用的 Azure Active Directory 的租户 ID。  从 " **Azure Active Directory"、"外部标识**" 下的 [Azure 门户](https://portal.azure.com)查找你的租户 ID。

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

* **[主权 cloud](reference-machine-learning-cloud-parity.md)**。 如果正在主权云中工作，则需要额外的代码向 Azure 进行身份验证。

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

* **使用现有的 Azure 资源**。  你还可以创建一个工作区，该工作区使用 Azure 资源 ID 格式的现有 Azure 资源。 在 Azure 门户或 SDK 中查找特定的 Azure 资源 Id。 此示例假设资源组、存储帐户、密钥保管库、App Insights 和容器注册表已存在。

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

有关详细信息，请参阅 [工作区 SDK 参考](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)。

如果在访问订阅时遇到问题，请参阅为 [Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)，以及 Azure 机器学习笔记本 [中的身份验证](https://aka.ms/aml-notebook-auth) 。

# <a name="portal"></a>[门户](#tab/azure-portal)

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
   | 存储帐户 | 工作区的默认存储帐户。 默认情况下，将创建一个新的。 |
   | Key Vault | 工作区使用的 Azure Key Vault。 默认情况下，将创建一个新的。 |
   | Application Insights | 工作区的 application insights 实例。 默认情况下，将创建一个新的。 |
   | 容器注册表 | 工作区的 Azure 容器注册表。 默认情况下， _不_ 会为工作区最初创建新的。 而是在训练或部署过程中创建 Docker 映像时，在需要时创建。 |

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
> 有关将专用终结点和虚拟网络与工作区结合使用的详细信息，请参阅 [网络隔离和隐私](how-to-network-security-overview.md)。


# <a name="python"></a>[Python](#tab/python)

Azure 机器学习 Python SDK 提供了 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py) 类，该类可用于 [工作区。创建 ( # B1 ](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) 来创建具有专用终结点的工作区。 此类需要现有虚拟网络。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 默认的网络配置是使用公用 __终结点，该终结点__ 可在公共 internet 上访问。 若要将对工作区的访问权限限制到已创建的 Azure 虚拟网络，可以改为选择 " __专用终结点__ " (预览 ") 作为 __连接方法__，然后使用" __+ 添加__ "配置终结点。   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="专用终结点选择":::  

1. 在 " __创建专用终结点__ " 窗体上，设置要使用的位置、名称和虚拟网络。 如果要将终结点用于专用 DNS 区域，请选择 " __与专用 DNS 区域集成__ "，并使用 " __专用 DNS 区域__ " 字段选择区域。 选择 __"确定"__ 以创建终结点。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="专用终结点创建":::   

1. 完成网络配置后，可以选择 "查看" 和 " __创建__"，或转到可选的 __高级__ 配置。

---

> [!IMPORTANT]  
> 将私有终结点用于 Azure 机器学习工作区当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。     
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="multiple-workspaces-with-private-endpoint"></a>具有专用终结点的多个工作区

创建专用终结点时，将创建一个名为 __privatelink.api.azureml.ms__ 的新专用 DNS 区域。 其中包含指向虚拟网络的链接。 如果在同一资源组中创建具有专用终结点的多个工作区，则仅可将第一个专用终结点的虚拟网络添加到 DNS 区域。 若要添加其他工作区/专用终结点使用的虚拟网络的条目，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择包含工作区的资源组。 然后选择名为 __privatelink.api.azureml.ms__ 的专用 DNS 区域资源
2. 在 " __设置__" 中，选择 " __虚拟网络链接__"。
3. 选择 __添加__ 。 从 " __添加虚拟网络" 链接__ 页，提供一个唯一的 __链接名称__，然后选择要添加的 __虚拟网络__ 。 选择 __"确定"__ 以添加网络链接。

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

### <a name="vulnerability-scanning"></a>漏洞扫描

Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 你应该允许 Azure 安全中心扫描你的资源并遵循其建议。 有关详细信息，请参阅安全中心和[Azure Kubernetes Services 与安全中心集成](../security-center/defender-for-kubernetes-introduction.md)的[azure 容器注册表映像扫描](../security-center/defender-for-container-registries-introduction.md)。

### <a name="advanced"></a>高级

默认情况下，工作区的元数据存储在 Microsoft 维护的 Azure Cosmos DB 实例中。 此数据使用 Microsoft 托管的密钥进行加密。

若要限制 Microsoft 在你的工作区中收集的数据，请在门户中选择 " __高业务影响" 工作区__ ，或 `hbi_workspace=true ` 在 Python 中进行设置。 有关此设置的详细信息，请参阅 [静态加密](concept-data-encryption.md#encryption-at-rest)。

> [!IMPORTANT]  
> 只有在创建工作区时，才能选择高业务影响。 在创建工作区后，不能更改此设置。   

#### <a name="use-your-own-key"></a>使用自己的密钥

你可以提供自己的密钥来加密数据。 这样做会创建在 Azure 订阅中存储元数据的 Azure Cosmos DB 实例。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

使用以下步骤来提供自己的密钥：

> [!IMPORTANT]  
> 在执行以下步骤之前，必须先执行以下操作：   
>
> 1. 在标识和访问管理) 中为订阅的 "参与者" 权限授权 __机器学习应用__ (。  
> 1. 按照 [将客户托管的密钥配置](../cosmos-db/how-to-setup-cmk.md) 为：
>     * 注册 Azure Cosmos DB 提供程序
>     * 创建和配置 Azure Key Vault
>     * 生成密钥
>   
>     您无需手动创建 Azure Cosmos DB 实例，而是在创建工作区时为您创建一个实例。 此 Azure Cosmos DB 实例将使用基于此模式的名称在单独的资源组中创建： `<your-workspace-resource-name>_<GUID>` 。   
>   
> 在创建工作区后，不能更改此设置。 如果删除工作区使用的 Azure Cosmos DB，则还必须删除正在使用该工作区的工作区。

# <a name="python"></a>[Python](#tab/python)

使用 `cmk_keyvault` 和 `resource_cmk_uri` 来指定客户管理的密钥。

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

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 选择 " __客户管理的密钥__"，然后选择 __"单击以选择密钥__"。

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="客户管理的密钥":::

1. 在 Azure Key Vault 窗体的 " __选择项__ " 窗体中，选择现有 Azure Key Vault、它包含的密钥以及密钥版本。 此密钥用于加密 Azure Cosmos DB 中存储的数据。 最后，使用 " __选择__ " 按钮使用此密钥。

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="选择密钥":::

---

### <a name="download-a-configuration-file"></a>下载配置文件

如果要创建[计算实例](tutorial-1st-experiment-sdk-setup.md#azure)，请跳过此步骤。  计算实例已创建此文件的副本。

# <a name="python"></a>[Python](#tab/python)

如果你计划使用引用此工作区 () 的本地环境中的代码 `ws` ，请编写配置文件：

```python
ws.write_config()
```

# <a name="portal"></a>[门户](#tab/azure-portal)

如果计划使用引用此工作区的本地环境中的代码，请从工作区的“概述”部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)

---

使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建计算实例时，此文件会添加到 VM 上的正确目录中。

## <a name="connect-to-a-workspace"></a>连接到工作区

在 Python 代码中，你将创建一个工作区对象以连接到工作区。  此代码将读取配置文件的内容以查找工作区。  如果尚未通过身份验证，则会提示你登录。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**多个租户。**  如果有多个帐户，请添加要使用的 Azure Active Directory 的租户 ID。  从 " **Azure Active Directory"、"外部标识**" 下的 [Azure 门户](https://portal.azure.com)查找你的租户 ID。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[主权 cloud](reference-machine-learning-cloud-parity.md)**。 如果正在主权云中工作，则需要额外的代码向 Azure 进行身份验证。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
如果在访问订阅时遇到问题，请参阅为 [Azure 机器学习资源和工作流设置身份验证](how-to-setup-authentication.md)，以及 Azure 机器学习笔记本 [中的身份验证](https://aka.ms/aml-notebook-auth) 。

## <a name="find-a-workspace"></a><a name="view"></a>查找工作区

查看你可以使用的所有工作区的列表。

# <a name="python"></a>[Python](#tab/python)

在 Azure 门户中的 " [订阅" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上查找你的订阅。  复制该 ID 并在下面的代码中使用它来查看可用于该订阅的所有工作区。

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在顶部搜索字段中，键入“机器学习”。  

1. 选择“机器学习”。

   ![搜索 Azure 机器学习工作区](./media/how-to-manage-workspace/find-workspaces.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择工作区即可显示其属性。

---


## <a name="delete-a-workspace"></a>创建工作区

当你不再需要某个工作区时，请将其删除。  

# <a name="python"></a>[Python](#tab/python)

删除工作区 `ws` ：

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

默认操作是不删除与工作区关联的资源，例如容器注册表、存储帐户、密钥保管库和 application insights。  `delete_dependent_resources`如果设置为 True，则也会删除这些资源。

# <a name="portal"></a>[门户](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com/)中，选择要删除的工作区顶部的“删除”。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="删除工作区":::

---

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>故障排除

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
* 使用 Azure 门户 [创建工作区和计算实例](tutorial-1st-experiment-sdk-setup.md)
* 使用 Python SDK [在自己的环境中创建工作区](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>后续步骤

拥有工作区后，请了解如何 [定型和部署模型](tutorial-train-models-with-aml.md)。