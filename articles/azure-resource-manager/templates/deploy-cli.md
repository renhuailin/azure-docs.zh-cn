---
title: 使用 Azure CLI 和模板部署资源
description: 使用 Azure 资源管理器和 Azure CLI 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 8ecb8bb2e6b24571d91e97157ff91ba931b0719d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296766"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>通过 ARM 模板和 Azure CLI 来部署资源

本文介绍了如何将 Azure CLI 与 Azure 资源管理器模板（ARM 模板）配合使用，以便将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅[模版部署概述](overview.md)。

部署命令在 Azure CLI 版本 2.2.0 中已更改。 本文中的示例需要 [Azure CLI 2.20.0 或更高版本](/cli/azure/install-azure-cli)。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果未安装 Azure CLI，可以使用 Azure Cloud Shell。 有关详细信息，请参阅[从 Azure Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、订阅、管理组或租户。 根据部署范围使用不同的命令。

* 若要部署到资源组，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)：

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* 若要部署到订阅，请使用 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)：

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* 若要部署到管理组，请使用 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)：

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  有关管理组级部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

* 若要部署到租户，请使用 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)：

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

对于每一个范围，部署模板的用户必须具有创建资源所必需的权限。

## <a name="deploy-local-template"></a>部署本地模板

可以部署本地计算机中的模板，也可以部署存储在外部的模板。 本节介绍如何部署本地模板。

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

若要部署本地模板，请在部署命令中使用 `--template-file` 参数。 以下示例还演示了如何设置参数值。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template> \
  --parameters storageAccountType=Standard_GRS
```

部署可能需要几分钟才能完成。 完成之后，会看到一条包含以下结果的消息：

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>部署远程模板

你可能更愿意将 ARM 模板存储在外部位置，而不是存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

若要部署外部模板，请使用 `template-uri` 参数。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果想要管理对模板的访问权限，请考虑使用[模板规格](#deploy-template-spec)。

若要使用存储在存储帐户中的相对路径部署远程链接模板，请使用 `query-string` 指定 SAS 令牌：

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

有关详细信息，请参阅[对链接模板使用相对路径](./linked-templates.md#linked-template)。

## <a name="deployment-name"></a>部署名称

部署 ARM 模板时，可以为部署指定名称。 此名称可以帮助你从部署历史记录中检索该部署。 如果没有为部署提供名称，将使用模板文件的名称。 例如，如果部署一个名为“azuredeploy.json”的模板，但未指定部署名称，则该部署将命名为“`azuredeploy`”。

每次运行部署时，一个包含部署名称的条目会添加到资源组的部署历史记录中。 如果运行另一个部署并为其指定了相同的名称，则会将先前的条目替换为当前部署。 如果要在部署历史记录中保持唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，你可以分配一个随机数。

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

或者，添加日期值。

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

如果使用相同的部署名称对同一资源组运行并发部署，则仅会完成最后一个部署。 尚未完成的具有相同名称的任何部署都将被最后一个部署所替换。 例如，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将仅部署一个存储帐户。 生成的存储帐户名为 `storage2`。

但是，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；在该部署完成时你又立即运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将有两个存储帐户。 一个名为 `storage1`，另一个名为 `storage2`。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一的名称时，可以并发运行它们而不会发生冲突。 如果你运行一个名为 `newStorage1` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你又运行了另一个名为 `newStorage2` 的部署，它部署了一个名为 `storage2` 的存储帐户，则部署历史记录中将有两个存储帐户和两个条目。

为避免与并发部署冲突并确保部署历史记录中的条目是唯一的，请为每个部署指定唯一的名称。

## <a name="deploy-template-spec"></a>部署模板规格

你可以创建[模板规格](template-specs.md)，而不是部署本地或远程模板。模板规格是 Azure 订阅中包含 ARM 模板的资源。 这使你可以轻松地与组织中的用户安全地共享模板。 可使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予对模板规格的访问权限。此功能目前以预览版提供。

下面的示例演示如何创建和部署模板规格。

首先，通过提供 ARM 模板创建模板规格。

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

然后，获取模板规格的 ID 并部署它。

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

有关详细信息，请参阅 [Azure 资源管理器模板规格](template-specs.md)。

## <a name="preview-changes"></a>预览更改

在部署模板之前，可以预览模板将对环境做出的更改。 使用[假设操作](./deploy-what-if.md)验证模板是否进行了预期的更改。 模拟操作还验证模板是否有错误。

## <a name="parameters"></a>parameters

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请在 `parameters` 中提供值。 例如，若要在 Bash shell 中将字符串和数组传递给模板，请使用：

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果要将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 配合使用，请以以下格式传递数组：`exampleArray="['value1','value2']"`。

还可以获取文件的内容并将该内容作为内联参数提供。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

_arrayContent.json_ 格式为：

```json
[
    "value1",
    "value2"
]
```

若要传入对象（例如用于设置标记），请使用 JSON。 例如，模板可能包含如下所示的参数：

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

在这种情况下，可以传入 JSON 字符串来设置参数，如以下 Bash 脚本中所示：

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

在要传递给对象的 JSON 两侧使用双引号。

可以使用变量来包含参数值。 在 Bash 中，将变量设置为所有参数值，并将其添加到部署命令。

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template> \
  --parameters $params
``` 

但是，如果将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 一起使用，请将变量设置为 JSON 字符串。 转义引号：`$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`。

### <a name="parameter-files"></a>参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 参数文件必须是本地文件。 Azure CLI 不支持外部参数文件。

有关参数文件的详细信息，请参阅[创建资源管理器参数文件](parameter-files.md)。

若要传递本地参数文件，请使用 `@` 指定名为 _storage.parameters.json_ 的本地文件。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters '@storage.parameters.json'
```

## <a name="handle-extended-json-format"></a>处理扩展 JSON 格式

若要使用 Azure CLI 2.3.0 或更低版本部署包含多行字符串或注释的模板，必须使用 `--handle-extended-json-format` 开关。  例如：

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>后续步骤

* 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](rollback-on-error.md)。
* 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
* 若要了解如何在模板中定义参数，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。