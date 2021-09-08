---
title: 在模板中使用部署脚本 | Microsoft Docs
description: 使用 Azure 资源管理器模板中的部署脚本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ece3693fa183ba31de569e7db632c3d294c10437
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187174"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>在 ARM 模板中使用部署脚本

了解如何使用 Azure 资源模板（ARM 模板）中的部署脚本。 使用名为 `Microsoft.Resources/deploymentScripts` 的新资源类型，用户可以在模板部署中执行脚本并查看执行结果。 这些脚本可用于执行自定义步骤，如：

- 将用户添加到目录
- 执行数据平面操作，例如，复制 blob 或种子数据库
- 查找并验证许可证密钥
- 创建自签名证书
- 在 Azure AD 中创建对象
- 从自定义系统中查找 IP 地址块

部署脚本的优势：

- 易于编码、使用和调试。 你可以在最喜欢的开发环境中开发部署脚本。 脚本可以嵌入模板或外部脚本文件中。
- 可以指定脚本语言和平台。 当前，支持 Linux 环境上的 Azure PowerShell 和 Azure CLI 部署脚本。
- 允许将命令行参数传递给脚本。
- 可以指定脚本输出，并将其传递回部署。

部署脚本资源仅在 Azure 容器实例可用的区域中可用。  请参阅 [Azure 容器实例在 Azure 区域的资源可用性](../../container-instances/container-instances-region-availability.md)。

> [!IMPORTANT]
> 脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，如果不指定，脚本服务会自动创建存储帐户和容器实例。 当部署脚本执行达到某个最终状态时，脚本服务通常会删除这两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

> [!NOTE]
> Azure 登录的重试逻辑现在内置于包装脚本中。 如果你在部署脚本所在的模板中授予权限，部署脚本服务会在 10 分钟内尝试以 10 秒的间隔进行登录，直到托管标识角色分配复制完毕。

## <a name="configure-the-minimum-permissions"></a>配置最低权限

对于部署脚本 API 2020-10-01 或更高版本，部署脚本的执行涉及两个主体：

- 部署主体（用于部署模板的主体）：此主体用于创建执行部署脚本资源所需的基础资源 - 存储帐户和 Azure 容器实例。 若要配置最小特权权限，请将具有以下属性的自定义角色分配给部署主体：

    ```json
    {
      "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
      "description": "Configure least privilege for the deployment principal in deployment script",
      "type": "customRole",
      "IsCustom": true,
      "permissions": [
        {
          "actions": [
            "Microsoft.Storage/storageAccounts/*",
            "Microsoft.ContainerInstance/containerGroups/*",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/deploymentScripts/*"
          ],
        }
      ],
      "assignableScopes": [
        "[subscription().id]"
      ]
    }
    ```

    如果尚未注册 Azure 存储和 Azure 容器实例资源提供程序，则你还需要添加 `Microsoft.Storage/register/action` 和 `Microsoft.ContainerInstance/register/action`。

- 部署脚本主体：只有在部署脚本需要向 Azure 进行身份验证并调用 Azure CLI/PowerShell 时，此主体才是必需的。 可通过两种方法指定部署脚本主体：

  - 在 `identity` 属性中指定用户分配的托管标识（请参阅[示例模板](#sample-templates)）。 指定后，脚本服务就会先调用 `Connect-AzAccount -Identity`，再调用部署脚本。 托管标识必须具有完成脚本中操作所需的访问权限。 目前，`identity` 属性仅支持用户分配的托管标识。 若要使用另一标识登录，请使用此列表中的第二种方法。
  - 将服务主体凭据作为安全的环境变量传递，然后即可在部署脚本中调用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 或 [az login](/cli/azure/reference-index?view=azure-cli-latest#az_login&preserve-view=true)。

  如果使用托管标识，则部署主体需要分配给托管标识资源的“托管的标识操作员”角色（内置角色）。

## <a name="sample-templates"></a>示例模板

以下 JSON 是一个示例。 有关详细信息，请参阅最新[模板架构](/azure/templates/microsoft.resources/deploymentscripts)。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 此示例用于演示目的。 `scriptContent` 和 `primaryScriptUri` 属性不能在模板中共存。

> [!NOTE]
> scriptContent 显示具有多行的脚本。  Azure 门户和 Azure DevOps 管道无法分析具有多行的部署脚本。 可以将 PowerShell 命令链接到一行（使用分号或 \\r\\n 或 \\n），或将 `primaryScriptUri` 属性与外部脚本文件一起使用 。 有许多免费的 JSON 字符串转义/反转义工具可用。 例如， [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html) 。

属性值详细信息：

- `identity`：对于部署脚本 API 版本 2020-10-01 或更高版本，用户分配的托管标识是可选的，除非你需要在脚本中执行任何特定于 Azure 的操作。  对于 API 版本 2019-10-01 预览版，需要托管标识，因为部署脚本服务使用该托管标识来执行脚本。 指定标识属性后，脚本服务就会先调用 `Connect-AzAccount -Identity` 再调用用户脚本。 当前，仅支持用户分配的托管标识。 若要使用其他标识登录，可以在脚本中调用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)。
- `kind`：指定脚本类型。 当前，支持 Azure PowerShell 和 Azure CLI 脚本。 值为 AzurePowerShell 和 AzureCLI 。
- `forceUpdateTag`：在模板部署之间更改此值将强制重新执行部署脚本。 如果使用 `newGuid()` 或 `utcNow()` 函数，则这两个函数只能在参数的默认值中使用。 若要了解详细信息，请参阅[多次运行脚本](#run-script-more-than-once)。
- `containerSettings`：指定该设置，用于自定义 Azure 容器实例。 部署脚本需要新的 Azure 容器实例。 无法指定现有的 Azure 容器实例。 但是，可以使用 `containerGroupName` 自定义容器组名称。 如果未指定，将自动生成组名。
- `storageAccountSettings`：指定设置以使用现有的存储帐户。 如果未指定 `storageAccountName`，将自动创建存储帐户。 请参阅[使用现有的存储帐户](#use-existing-storage-account)。
- `azPowerShellVersion`/`azCliVersion`：指定要使用的模块版本。 查看[支持的 Azure PowerShell 版本](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)的列表。 查看[支持的 Azure CLI 版本](https://mcr.microsoft.com/v2/azure-cli/tags/list)的列表。

  >[!IMPORTANT]
  > 部署脚本使用 Microsoft Container Registry (MCR) 中可用的 CLI 映像。 认证部署脚本的 CLI 映像大约需要一个月的时间。 不要使用 30 天内发布的 CLI 版本。 若要查找映像的发行日期，请参阅 [Azure CLI 发行说明](/cli/azure/release-notes-azure-cli)。 如果使用了不受支持的版本，错误消息中会列出受支持的版本。

- `arguments`：指定参数值。 请以空格分隔这些值。

  部署脚本通过发出 [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) 系统调用，将参数拆分为字符串数组。 此步骤是必要的，因为参数作为[命令属性](/rest/api/container-instances/containergroups/createorupdate#containerexec)传递给 Azure 容器实例，而命令属性是字符串数组。

  如果参数包含转义字符，请使用 [JsonEscaper](https://www.jsonescaper.com/) 对字符进行双重转义。 将最初已转义的字符串粘贴到工具中，然后选择“转义”。  该工具会输出一个经过双重转义处理的字符串。 例如，在前面的示例模板中，参数是 `-name \"John Dole\"`。 转义字符串是 `-name \\\"John Dole\\\"`。

  若要将 object 类型的 ARM 模板参数作为参数传递，请使用 [string()](./template-functions-string.md#string) 函数将对象转换为字符串，然后使用 [replace()](./template-functions-string.md#replace) 函数将任何 `\"` 替换为 `\\\"`。 例如：

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  有关详细信息，请参阅[示例模板](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)。

- `environmentVariables`：指定环境变量以传递到脚本。 有关详细信息，请参阅[开发部署脚本](#develop-deployment-scripts)。
- `scriptContent`：指定脚本内容。 若要运行外部脚本，请改用 `primaryScriptUri`。 有关示例，请参阅[使用内联脚本](#use-inline-scripts)和[使用外部脚本](#use-external-scripts)。
- `primaryScriptUri`：为具有受支持的文件扩展名的主部署脚本指定一个可公开访问的 URL。 有关详细信息，请参阅[使用外部脚本](#use-external-scripts)。
- `supportingScriptUris`：指定一个可公开访问的 URL 数组，它们指向在 `scriptContent` 或 `primaryScriptUri` 中调用的支持性文件。 有关详细信息，请参阅[使用外部脚本](#use-external-scripts)。
- `timeout`：指定 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)中指定的脚本执行最大允许时间。 默认值为 **P1D**。
- `cleanupPreference`. 指定在脚本执行达到最终状态时清理部署资源的首选项。 默认设置为 Always，这意味着不管最终状态如何（成功、失败、已取消），都会删除资源。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。
- `retentionInterval`：指定在部署脚本执行达到最终状态后，服务保留部署脚本资源的时间间隔。 在此持续时间到期时，将删除部署脚本资源。 持续时间基于 [ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 保留时间间隔为 1 到 26 小时 (PT26H)。 当 `cleanupPreference` 设置为 **OnExpiration** 时将使用此属性。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他示例

- [示例 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)：创建一个密钥保管库，并使用部署脚本将证书分配给该保管库。
- [示例 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)：在订阅级别创建一个资源组，在该资源组中创建一个密钥保管库，然后使用部署脚本将证书分配给该保管库。
- [示例 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)：创建一个用户分配的托管标识，在资源组级别将参与者角色分配给该标识，创建一个密钥保管库，然后使用部署脚本将证书分配给该保管库。

## <a name="use-inline-scripts"></a>使用内联脚本

以下模板具有一个使用 `Microsoft.Resources/deploymentScripts` 类型定义的资源。 突出显示的部分是内联脚本。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> 由于内联部署脚本是用双引号括起来的，因此部署脚本内的字符串需要使用反斜杠 (&#92;) 进行转义或者使用单引号括起来。 还可以考虑使用字符串替换，如先前的 JSON 示例所示。

该脚本采用一个参数，并输出参数值。 DeploymentScriptOutputs 用于存储输出`DeploymentScriptOutputs`。 在输出部分，值行显示了如何访问存储的值`value`。 `Write-Output` 用于调试目的。 若要了解如何访问输出文件，请参阅[对部署脚本进行监视和故障排除](#monitor-and-troubleshoot-deployment-scripts)。 有关属性说明，请参阅[示例模板](#sample-templates)。

若要运行脚本，请选择“尝试”以打开 Cloud Shell，然后将以下代码粘贴到 Shell 窗格中。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

输出如下所示：

![资源管理器模板部署脚本 hello world 输出](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>使用外部脚本

除了内联脚本以外，还可以使用外部脚本文件。 仅支持文件扩展名为 ps1 的主 PowerShell 脚本。 对于 CLI 脚本，主脚本可以具有任何扩展名（或没有扩展名），只要这些脚本是有效的 bash 脚本。 若要使用外部脚本文件，请将 `scriptContent` 替换为 `primaryScriptUri`。 例如：

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

有关详细信息，请参阅[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)。

外部脚本文件必须是可访问的。 若要保护存储在 Azure 存储帐户中的脚本文件，请生成 SAS 令牌，并将其包含在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。 有关详细信息，请参阅[使用 SAS 令牌部署专用 ARM 模板](./secure-template-with-sas-token.md)。

你负责确保部署脚本（`primaryScriptUri` 或 `supportingScriptUris`）所引用的脚本的完整性。 仅引用你信任的脚本。

## <a name="use-supporting-scripts"></a>使用支持脚本

可以将复杂的逻辑分成一个或多个支持脚本文件。 使用 `supportingScriptUris` 属性可根据需要向支持脚本文件提供 URI 数组：

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

可从内联脚本和主脚本文件中调用支持脚本文件。 支持脚本文件对文件扩展名没有限制。

支持文件在运行时复制到 `azscripts/azscriptinput`。 使用相对路径从内联脚本和主脚本文件中引用支持文件。

## <a name="work-with-outputs-from-powershell-script"></a>处理 PowerShell 脚本的输出

以下模板显示了如何在两个 `deploymentScripts` 资源之间传递值：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

在第一个资源中，定义一个名为 $DeploymentScriptOutputs 的变量，并使用它来存储输出值`$DeploymentScriptOutputs`。 若要访问模板内另一个资源的输出值，请使用：

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 脚本的输出

与 PowerShell 部署脚本不同，CLI/bash 支持不通过公开常见变量来存储脚本输出，而使用名为 `AZ_SCRIPTS_OUTPUT_PATH` 的环境变量来存储脚本输出文件所在的位置。 如果从资源管理器模板运行部署脚本，则 Bash shell 会自动设置此环境变量。 `AZ_SCRIPTS_OUTPUT_PATH` 的值为 /mnt/azscripts/azscriptoutput/scriptoutputs.json。

部署脚本输出必须保存在 `AZ_SCRIPTS_OUTPUT_PATH` 位置，并且输出必须是有效的 JSON 字符串对象。 必须将该文件的内容保存为键值对。 例如，字符串数组存储为 `{ "MyResult": [ "foo", "bar"] }`。  仅存储数组结果是无效的，例如 `[ "foo", "bar" ]`。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

在先前的示例中使用了 [jq](https://stedolan.github.io/jq/)。 它带有容器映像。 请参阅[配置开发环境](#configure-development-environment)。

## <a name="use-existing-storage-account"></a>使用现有存储帐户

脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，如果不指定，脚本服务会自动创建存储帐户和容器实例。 使用现有存储帐户的要求：

- 支持的存储帐户类型如下：

    | SKU             | 支持的类型     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage、StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage、StorageV2 |
    | Standard_RAGRS  | Storage、StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    这些组合支持文件共享。 有关详细信息，请参阅[创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)和[存储帐户类型](../../storage/common/storage-account-overview.md)。

- 尚不支持存储帐户防火墙规则。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。
- 部署主体必须具有管理存储帐户的权限，包括读取、创建和删除文件共享。

若要指定现有存储帐户，请将以下 JSON 添加到 `Microsoft.Resources/deploymentScripts` 的属性元素中：

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`：指定存储帐户的名称。
- `storageAccountKey`：指定存储帐户密钥之一。 可以使用 [listKeys()](./template-functions-resource.md#listkeys) 函数检索密钥。 例如：

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

有关完整的 `Microsoft.Resources/deploymentScripts` 定义示例，请参阅[示例模板](#sample-templates)。

使用现有存储帐户时，脚本服务将创建一个具有唯一名称的文件共享。 有关脚本服务清理文件共享的方式，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="develop-deployment-scripts"></a>开发部署脚本

### <a name="handle-non-terminating-errors"></a>处理非终止错误

可通过在部署脚本中使用 $ErrorActionPreference 变量来控制 PowerShell 响应非终止错误的方式`$ErrorActionPreference`。 如果未在部署脚本中设置该变量，则脚本服务将使用默认值“Continue”。

不管为 `$ErrorActionPreference` 设置了什么值，当脚本遇到错误时，脚本服务会将资源预配状态设置为“失败”。

### <a name="use-environment-variables"></a>使用环境变量

部署脚本使用下列环境变量：

|环境变量|默认值|系统预留|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell：userscript.ps1；Azure CLI：userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.json|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.json|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

有关使用 `AZ_SCRIPTS_OUTPUT_PATH` 的详细信息，请参阅[使用 CLI 脚本中的输出](#work-with-outputs-from-cli-script)。

### <a name="pass-secured-strings-to-deployment-script"></a>将安全字符串传递到部署脚本

通过在容器实例中设置环境变量 (EnvironmentVariable)，可为容器运行的应用程序或脚本提供动态配置。 部署脚本以与 Azure 容器实例相同的方式处理非安全和安全环境变量。 有关详细信息，请参阅[在容器实例中设置环境变量](../../container-instances/container-instances-environment-variables.md#secure-values)。 有关示例，请参阅[示例模板](#sample-templates)。

环境变量允许的最大大小为 64 KB。

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>对部署脚本进行监视和故障排除

脚本服务将创建一个[存储帐户](../../storage/common/storage-account-overview.md)（除非指定一个现有的存储帐户）和一个[容器实例](../../container-instances/container-instances-overview.md)以执行脚本。 如果这些资源是由脚本服务自动创建的，则两个资源的资源名称后缀均为 azscripts`azscripts`。

![资源管理器模板部署脚本资源名称](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

用户脚本、执行结果和 stdout 文件存储在存储帐户的文件共享中。 有一个名为 `azscripts` 的文件夹。 在该文件夹中，还有两个用于输入文件和输出文件的文件夹：azscriptinput 和 azscriptoutput`azscriptinput` `azscriptoutput`。

输出文件夹包含 _executionresult.json_ 和脚本输出文件。 可以在 executionresult.json 中看到脚本执行错误消息。 仅当成功执行脚本时，才会创建输出文件。 输入文件夹包含一个系统 PowerShell 脚本文件和一些用户部署脚本文件。 可以使用修订后的文件替换用户部署脚本文件，然后从 Azure 容器实例重新运行部署脚本。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

部署好部署脚本资源后，该资源会在 Azure 门户中的资源组下列出。 以下屏幕截图显示了部署脚本资源的“概述”页面：

![资源管理器模板部署脚本门户概述](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

“概述”页面显示了资源的一些重要信息，例如预配状态、存储帐户、容器实例和日志   。

从左侧菜单中，可查看部署脚本内容、传递给脚本的参数以及输出。 还可导出部署脚本的模板，包括部署脚本本身。

### <a name="use-powershell"></a>使用 PowerShell

通过 Azure PowerShell，可在订阅或资源组范围管理部署脚本：

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript)：获取或列出部署脚本。
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog)：获取部署脚本执行的日志。
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript)：删除部署脚本及其关联资源。
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog)：将部署脚本执行的日志保存到磁盘。

`Get-AzDeploymentScript` 输出类似于：

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>使用 Azure CLI

通过 Azure CLI，可在订阅或资源组范围管理部署脚本：

- [az deployment-scripts delete](/cli/azure/deployment-scripts#az_deployment_scripts_delete)：删除部署脚本。
- [az deployment-scripts list](/cli/azure/deployment-scripts#az_deployment_scripts_list)：列出所有部署脚本。
- [az deployment-scripts show](/cli/azure/deployment-scripts#az_deployment_scripts_show)：检索部署脚本。
- [az deployment-scripts show-log](/cli/azure/deployment-scripts#az_deployment_scripts_show_log)：显示部署脚本日志。

list 命令输出如下所示：

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>使用 Rest API

可以使用 REST API 在资源组级别和订阅级别获取部署脚本资源部署信息：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

下面的示例使用 [ARMClient](https://github.com/projectkudu/ARMClient)：

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

输出类似于：

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

以下 REST API 返回日志：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

它仅在删除部署脚本资源之前有效。

要在门户中查看 deploymentScripts 资源，请选择“显示隐藏的类型”：

![资源管理器模板部署脚本、显示隐藏的类型、门户](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清理部署脚本资源

脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，否则脚本服务会自动创建存储帐户和容器实例。 当部署脚本执行达到某个最终状态时，脚本服务会删除这两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 有关定价信息，请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)和 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

这些资源的生命周期由模板中的以下属性控制：

- `cleanupPreference`：当脚本执行达到最终状态时清理首选项。 支持的值包括：

  - **Always**：脚本执行达到最终状态后，将删除自动创建的资源。 如果使用现有存储帐户，脚本服务将删除在存储帐户中创建的文件共享。 由于在清理资源后，`deploymentScripts` 资源可能仍然存在，因此在删除资源之前，脚本服务将保留脚本执行结果，例如 stdout、输出和返回值。
  - **OnSuccess**：仅当脚本执行成功时才删除自动创建的资源。 如果使用现有存储帐户，则脚本服务仅在脚本执行成功时才删除文件共享。 你仍可以访问资源来查找调试信息。
  - **OnExpiration**：仅当 `retentionInterval` 设置过期时，才删除自动创建的资源。 如果使用现有存储帐户，脚本服务将删除文件共享，但保留存储帐户。

- `retentionInterval`：指定将保留脚本资源的时间间隔，超过此时间间隔后，脚本资源将过期并被删除。

> [!NOTE]
> 不建议将脚本服务生成的存储帐户和容器实例用于其他目的。 根据脚本的生命周期，可能会删除这两个资源。

容器实例和存储帐户根据 `cleanupPreference` 被删除。 但是，如果脚本失败，并且 `cleanupPreference` 未设置为“始终”，则部署过程会自动使容器运行一小时。 可以使用这一小时对脚本进行故障排除。 如果要在成功部署后保持容器运行，请向脚本添加睡眠步骤。 例如，将 [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) 添加到脚本的末尾。 如果你未添加睡眠步骤，则容器将被设置为终端状态，即使其尚未被删除，你也无法访问该容器。

## <a name="run-script-more-than-once"></a>多次运行脚本

部署脚本执行操作是一个幂等操作。 如果未更改 `deploymentScripts` 资源属性（包括内联脚本），则在重新部署模板时不会执行该脚本。 部署脚本服务将模板中的资源名称与同一资源组中的现有资源进行比较。 如果要多次执行相同的部署脚本，有两个选项可供选择：

- 更改 `deploymentScripts` 资源的名称。 例如，使用 [utcNow](./template-functions-date.md#utcnow) 模板函数作为资源名称或资源名称的一部分。 更改资源名称将创建一个新的 `deploymentScripts` 资源。 这对于保留脚本执行历史记录很有帮助。

    > [!NOTE]
    > `utcNow` 函数只能在参数的默认值中使用。

- 在 `forceUpdateTag` 模板属性中指定其他值。 例如，使用 `utcNow` 作为值。

> [!NOTE]
> 编写幂等的部署脚本。 这样可以确保即使它们再次意外运行，也不会引起系统更改。 例如，如果使用部署脚本创建 Azure 资源，请在创建资源之前验证该资源确实不存在，以便脚本成功执行，否则不必再次创建该资源。

## <a name="configure-development-environment"></a>配置开发环境

可使用预配置的容器映像作为部署脚本开发环境。 有关详细信息，请参阅[为模板中的部署脚本配置开发环境](./deployment-script-template-configure-dev.md)。

成功测试脚本后，可以将其用作模板中的部署脚本。

## <a name="deployment-script-error-codes"></a>部署脚本错误代码

| 错误代码 | 说明 |
|------------|-------------|
| DeploymentScriptInvalidOperation | 模板中的部署脚本资源定义包含无效的属性名。 |
| DeploymentScriptResourceConflict | 无法删除处于非终端状态且执行未超过 1 小时的部署脚本资源。 或者，无法同时重新运行资源标识符相同（订阅、资源组名称和资源名称相同）但脚本正文内容不同的同一部署脚本。 |
| DeploymentScriptOperationFailed | 部署脚本操作在内部失败。 请联系 Microsoft 支持部门。 |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | 尚未为现有存储帐户指定访问密钥。|
| DeploymentScriptContainerGroupContainsInvalidContainers | 由部署脚本服务创建的容器组已从外部修改，并且添加了无效的容器。 |
| DeploymentScriptContainerGroupInNonterminalState | 两个或更多部署脚本资源在同一资源组中使用相同的 Azure 容器实例名称，其中一个尚未完成其执行。 |
| DeploymentScriptStorageAccountInvalidKind | BlobBlobStorage 或 BlobStorage 类型的现有存储帐户不支持文件共享，因此无法使用。 |
| DeploymentScriptStorageAccountInvalidKindAndSku | 现有存储帐户不支持文件共享。 有关支持的存储帐户类型的列表，请参阅[使用现有存储帐户](#use-existing-storage-account)。 |
| DeploymentScriptStorageAccountNotFound | 存储帐户不存在，或者已被外部进程或工具删除。 |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | 指定的存储帐户包含一个服务终结点。 不支持具有服务终结点的存储帐户。 |
| DeploymentScriptStorageAccountInvalidAccessKey | 为现有存储帐户指定的访问密钥无效。 |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | 存储帐户密钥格式无效。 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。 |
| DeploymentScriptExceededMaxAllowedTime | 部署脚本执行时间超过了部署脚本资源定义中指定的超时值。 |
| DeploymentScriptInvalidOutputs | 部署脚本输出不是有效的 JSON 对象。 |
| DeploymentScriptContainerInstancesServiceLoginFailure | 用户分配的托管标识在间隔 1 分钟的 10 次尝试后无法登录。 |
| DeploymentScriptContainerGroupNotFound | 部署脚本服务创建的容器组已被外部工具或进程删除。 |
| DeploymentScriptDownloadFailure | 未能下载支持脚本。 请参阅[使用支持脚本](#use-supporting-scripts)。|
| DeploymentScriptError | 用户脚本引发错误。 |
| DeploymentScriptBootstrapScriptExecutionFailed | 启动脚本引发错误。 启动脚本是协调部署脚本执行的系统脚本。 |
| DeploymentScriptExecutionFailed | 部署脚本执行期间出现未知错误。 |
| DeploymentScriptContainerInstancesServiceUnavailable | 创建 Azure 容器实例 (ACI) 时，ACI 引发了“服务不可用”错误。 |
| DeploymentScriptContainerGroupInNonterminalState | 创建 Azure 容器实例 (ACI) 时，另一个部署脚本正在同一范围中使用相同的 ACI 名称（相同的订阅、资源组名称和资源名称）。 |
| DeploymentScriptContainerGroupNameInvalid | 指定的 Azure 容器实例名称 (ACI) 不符合 ACI 要求。 请参阅[排查 Azure 容器实例中的常见问题](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)。|

## <a name="next-steps"></a>后续步骤

本教程已介绍部署脚本的用法。 完成部署脚本教程：

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板中的部署脚本](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Learn 模块：使用部署脚本扩展 ARM 模板](/learn/modules/extend-resource-manager-template-deployment-scripts/)
