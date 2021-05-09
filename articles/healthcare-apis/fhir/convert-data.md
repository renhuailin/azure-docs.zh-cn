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
ms.openlocfilehash: c796b72da15cb6278c355ed86fdf9eaaf54ca2be
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108794478"
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

强烈建议在 ACR 上托管自己的模板副本。 托管自己的模板副本以及使用 $convert 操作中涉及四个步骤：

1. 向 Azure 容器注册表推送模板。
1. 在 Azure API for FHIR 实例上启用托管标识。
1. 提供 ACR 对 Azure API for FHIR 托管标识的访问权限。
1. 在 Azure API for FHIR 中注册 ACR 服务器。
1. （可选）配置 ACR 防火墙以确保安全访问。

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

可以使用 Azure 门户 或 CLI 注册 ACR 服务器。

#### <a name="registering-the-acr-server-using-azure-portal"></a>使用 Azure 门户 注册 ACR 服务器
导航到实例 _中_ "数据 _转换"_ 下的"项目Azure API for FHIR边栏选项卡。 你将看到当前注册的 ACR 服务器的列表。 选择 _"_ 添加"，然后从下拉列表中选择注册表服务器。 需要选择"保存 _"，_ 注册生效。 应用更改并重启实例可能需要几分钟时间。

#### <a name="registering-the-acr-server-using-cli"></a>使用 CLI 注册 ACR 服务器
可以在客户端中注册最多 20 个 ACR Azure API for FHIR。

如果需要，请从 Azure PowerShell 安装 healthcareapis CLI：

```powershell
az extension add -n healthcareapis
```

按照以下示例，将 ACR 服务器注册到 Azure API for FHIR：

##### <a name="register-a-single-acr-server"></a>注册一个 ACR 服务器

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>注册多个 ACR 服务器

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>配置 ACR 防火墙

从 **门户** 中选择"Azure 存储帐户的网络"。

   :::image type="content" source="media/convert-data/networking-container-registry.png" alt-text="容器注册表。":::


选择“所选网络”。 

在" **防火墙"** 部分下，在"地址范围"框中 **指定 IP** 地址。 添加 IP 范围以允许从 Internet 或本地网络访问。 

在下表中，你将找到预配托管服务的 Azure Azure API for FHIR IP 地址。

|**Azure 区域**         |**公共 IP 地址** |
|:----------------------|:-------------------|
| 澳大利亚东部       | 20.53.44.80       |
| 加拿大中部       | 20.48.192.84      |
| 美国中部           | 52.182.208.31     |
| 美国东部              | 20.62.128.148     |
| 美国东部 2            | 20.49.102.228     |
| 美国东部 2 EUAP       | 20.39.26.254      |
| 德国北部        | 51.116.51.33      |
| 德国中西部 | 51.116.146.216    |
| Japan East           | 20.191.160.26     |
| 韩国中部        | 20.41.69.51       |
| 美国中北部     | 20.49.114.188     |
| 北欧         | 52.146.131.52     |
| 南非北部   | 102.133.220.197   |
| 美国中南部     | 13.73.254.220     |
| 东南亚       | 23.98.108.42      |
| 瑞士北部    | 51.107.60.95      |
| 英国南部             | 51.104.30.170     |
| 英国西部              | 51.137.164.94     |
| 美国中西部      | 52.150.156.44     |
| 西欧          | 20.61.98.66       |
| 美国西部 2            | 40.64.135.77      |


> [!NOTE]
> 上述步骤类似于如何导出 FHIR 数据文档中所述的配置步骤。  有关详细信息，请参阅安全 [导出到Azure 存储](https://docs.microsoft.com/azure/healthcare-apis/fhir/export-data#secure-export-to-azure-storage)

### <a name="verify"></a>Verify

调用 $convert-data API，在 templateCollectionReference 参数中指定模板引用。

`<RegistryServer>/<imageName>@<imageDigest>`
