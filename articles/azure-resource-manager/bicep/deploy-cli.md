---
title: 使用 Azure CLI 和和 Bicep 文件部署资源
description: 使用 Azure 资源管理器和 Azure CLI 将资源部署到 Azure。 Bicep 文件中定义了资源。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 5e46b920359615c34864d670363b11c451b416b3
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295125"
---
# <a name="deploy-resources-with-bicep-and-azure-cli"></a>使用 Bicep 和 Azure CLI 部署资源

本文介绍如何将 Azure CLI 与 Bicep 文件配合使用将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅 [Bicep 概述](./overview.md)。

若要部署 Bicep 文件，需要安装 [Azure CLI 2.20.0 或更高版本](/cli/azure/install-azure-cli)。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果未安装 Azure CLI，可以使用 Azure Cloud Shell。 有关详细信息，请参阅[从 Azure Cloud Shell 部署 Bicep 文件](./deploy-cloud-shell.md)。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、订阅、管理组或租户。 根据部署范围使用不同的命令。

* 若要部署到资源组，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)：

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* 若要部署到订阅，请使用 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)：

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* 若要部署到管理组，请使用 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)：

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  有关管理组级部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

* 若要部署到租户，请使用 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)：

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

对于每个范围，部署 Bicep 文件的用户必须拥有创建资源所需的权限。

## <a name="deploy-local-bicep-file"></a>部署本地 Bicep 文件

可以部署本地计算机中的 Bicep 文件，也可以部署存储在外部的模板。 本节介绍如何部署本地 Bicep 文件。

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

若要部署本地 Bicep 文件，请在部署命令中使用 `--template-file` 参数。 以下示例还演示了如何设置参数值。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

部署可能需要几分钟才能完成。 完成之后，会看到一条包含以下结果的消息：

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>部署远程 Bicep 文件

目前，Azure CLI 不支持部署远程 Bicep 文件。 使用 [Bicep CLI](./install.md#development-environment) 将 Bicep 文件编译为 JSON 模板，然后将 JSON 文件加载到远程位置。

## <a name="parameters"></a>parameters

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请在 `parameters` 中提供值。 例如，若要在 Bash shell 中将字符串和数组传递给 Bicep 文件，请使用：

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果要将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 配合使用，请以以下格式传递数组：`exampleArray="['value1','value2']"`。

还可以获取文件的内容并将该内容作为内联参数提供。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
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

若要传入对象（例如用于设置标记），请使用 JSON。 例如，Bicep 文件可能包含如下所示的参数：

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
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

在要传递给对象的 JSON 两侧使用双引号。

可以使用变量来包含参数值。 在 Bash 中，将变量设置为所有参数值，并将其添加到部署命令。

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
``` 

但是，如果将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 一起使用，请将变量设置为 JSON 字符串。 转义引号：`$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`。

### <a name="parameter-files"></a>参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 参数文件必须是本地文件。 Azure CLI 不支持外部参数文件。 Bicep 文件使用 JSON 参数文件。

有关参数文件的详细信息，请参阅[创建资源管理器参数文件](./parameter-files.md)。

若要传递本地参数文件，请使用 `@` 指定名为 _storage.parameters.json_ 的本地文件。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>预览更改

在部署 Bicep 文件之前，可以预览 Bicep 文件将对环境做出的更改。 使用[假设操作](./deploy-what-if.md)验证 Bicep 文件是否进行预期更改。 假设操作还验证 Bicep 文件是否有错误。

## <a name="deploy-template-specs"></a>部署模板规格

目前，Azure CLI 不支持通过提供 Bicep 文件来创建模板规格。 但是，可以使用 [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) 资源创建 Bicep 文件来部署模板规格。此处有一个[示例](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)。 还可使用 Bicep CLI 将 Bicep 文件生成到 ARM 模板 JSON，然后使用 JSON 模板创建模板规格。

## <a name="deployment-name"></a>部署名称

部署 Bicep 文件时，可以为部署指定名称。 此名称可以帮助你从部署历史记录中检索该部署。 如果没有为部署提供名称，将使用 Bicep 文件的名称。 例如，如果部署一个名为 `azuredeploy.bicep` 的 Bicep 文件，但未指定部署名称，则该部署将命名为 `azuredeploy`。

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

## <a name="next-steps"></a>后续步骤

* 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](../templates/rollback-on-error.md)。
- 若要了解如何在文件中定义参数，请参阅[了解 Bicep 文件的结构和语法](file.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](../templates/common-deployment-errors.md)。