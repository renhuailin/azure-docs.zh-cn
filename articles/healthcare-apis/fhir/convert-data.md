---
title: Azure API for FHIR 的数据转换
description: 使用 $convert-data 终结点和自定义转换器模板来转换 Azure API for FHIR 中的数据。
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017919"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>如何将数据转换为 FHIR（预览版）

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure API for FHIR 中的 $convert-data 自定义终结点用于将数据从不同格式转换为 FHIR。 它使用 Liquid 模板引擎和来自 [FHIR 转换器](https://github.com/microsoft/FHIR-Converter)项目的模板作为默认模板。 可根据需要自定义这些转换模板。 目前，它支持 HL7v2 到 FHIR 的转换。

## <a name="use-the-convert-data-endpoint"></a>使用 $convert-data 终结点

`https://<<FHIR service base URL>>/$convert-data`

$convert-data 采用请求正文中的[参数](http://hl7.org/fhir/parameters.html)资源，如下所述：

**参数资源：**

| 参数名称      | 说明 | 接受的值 |
| ----------- | ----------- | ----------- |
| inputData      | 要转换的数据。 | JSON 字符串数据类型的有效值|
| inputDataType   | 输入的数据类型。 | ```HL7v2``` |
| templateCollectionReference | 对模板集合的引用。 可以是对默认模板的引用，也可以是注册到 Azure API for FHIR 的自定义模板映像。 请查看以下信息，了解如何自定义模板、在 ACR 上托管这些模板，以及如何注册到 Azure API for FHIR。  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | 转换数据时要使用的根模板。 | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> 默认模板有助于你快速入门。 不过，在我们升级 Azure API for FHIR 时，可能会更新这些模板。 若要在不同版本的 Azure API for FHIR 之间保持一致的数据转换行为，必须在 Azure 容器注册表上托管自己的模板副本，将这些模板注册到 Azure API for FHIR，并在稍后所述的 API 调用中使用。

**示例请求：**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**示例响应：**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>自定义模板

可使用 Visual Studio Code 的 [FHIR 转换器扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)根据需要自定义模板。 此扩展提供交互式编辑体验，让你能够轻松地下载 Microsoft 发布的模板和示例数据。 有关详细信息，请参阅扩展中的文档。

## <a name="host-and-use-templates"></a>托管并使用模板

强烈建议在 ACR 上托管自己的模板副本。 托管自己的模板副本和在 $convert-data 操作中使用这些模板涉及 4 个步骤：

1. 向 Azure 容器注册表推送模板。
1. 在 Azure API for FHIR 实例上启用托管标识。
1. 提供 ACR 对 Azure API for FHIR 托管标识的访问权限。
1. 在 Azure API for FHIR 中注册 ACR 服务器。

### <a name="push-templates-to-azure-container-registry"></a>向 Azure 容器注册表推送模板

创建 ACR 实例后，可使用[FHIR 转换器扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)中的“FHIR 转换器: 推送模板”命令将自定义模板推送到 ACR。 另外，还可使用[模板管理 CLI 工具](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md)来实现此目的。

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上启用托管标识

在 Azure 门户中转到 Azure API for FHIR 服务的实例，然后选择“标识”边栏选项卡。
将状态更改为“开”，以在 Azure API for FHIR 中启用托管标识。

![启用托管标识](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>提供 ACR 对 Azure API for FHIR 的访问权限

导航到 ACR 实例中的“访问控制(IAM)”边栏选项卡，然后选择“添加角色分配”。

![ACR 角色分配](media/convert-data/fhir-acr-role-assignment.png)

向 Azure API for FHIR 服务实例授予 AcrPull 角色。

![添加角色](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中注册 ACR 服务器

最多可在 Azure API for FHIR 中注册 20 个 ACR 服务器。

如果需要，请从 Azure PowerShell 安装 healthcareapis CLI：

```powershell
az extension add -n healthcareapis
```

按照以下示例，将 ACR 服务器注册到 Azure API for FHIR：

#### <a name="register-a-single-acr-server"></a>注册一个 ACR 服务器

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>注册多个 ACR 服务器

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Verify

调用 $convert-data API，在 templateCollectionReference 参数中指定模板引用。

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>已知问题和解决方法

- 某些默认模板文件包含 UTF-8 BOM。 因此，生成的 ID 值将包含 BOM 字符。 这可能会导致 FHIR 服务器出现问题。 解决方法是使用 VS Code 扩展请求 Microsoft 模板，并在从 ID/Procedure.liquid、ID/Provenance.liquid 和 ID/Immunization.liquid 中删除 BOM 字符后，将这些模板推送到你自己的 ACR  。

