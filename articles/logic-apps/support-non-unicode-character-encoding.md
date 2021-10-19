---
title: 转换非 Unicode 编码的文本以实现兼容性
description: 通过使用 base64 编码和 Azure Functions 将文本有效负载转换为 UTF-8，以此来处理 Azure 逻辑应用程序中的非 Unicode 字符。
ms.date: 10/05/2021
ms.topic: how-to
ms.reviewer: estfan, azla
ms.service: logic-apps
ms.openlocfilehash: 1251a1622e62b7940c25ac810db10f70da560000
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618983"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>在逻辑应用中支持非 Unicode 字符编码

处理文本有效负载时，Azure 逻辑应用会推断以 Unicode 格式（如 UTF-8）编码的文本。 在工作流中接收、发送或处理使用不同编码的字符时，可能会遇到问题。 例如，使用不支持 Unicode 的旧系统时，平面文件中可能会出现损坏的字符。

若要处理使用其他字符编码的文本，请将 base64 编码应用于非 Unicode 有效负载。 此步骤可防止逻辑应用假定文本采用 UTF-8 格式。 然后，可以使用 Azure Functions 将所有 .NET 支持编码转换为 UTF-8 格式。 

此解决方案适用于多租户和单租户工作流。 你还可以[将此解决方案用于 AS2 连接器](#convert-payloads-for-as2)。

## <a name="convert-payload-encoding"></a>转换有效负载编码

首先，检查触发器能否正确识别内容类型。 此步骤可确保逻辑应用不再假定文本是 UTF-8 格式。 

在具有“推断内容类型”属性的触发器和操作中，选择“否”。   通常可以在操作的“添加参数”列表中找到此属性。 但是，如果操作不包括此属性，则内容类型由入站消息设置。

在下面的列表显示的连接器类型中，可以禁用自动推断内容类型这一功能：
* [OneDrive](/connectors/onedrive/)
* [Azure Blob 存储](/connectors/azureblob/)
* [Azure 文件存储](/connectors/azurefile/)
* [文件系统](/connectors/filesystem/)
* [Google Drive](/connectors/googledrive/)
* [SFTP - SSH](/connectors/sftpwithssh/)
 
如果对 `text/plain` 内容使用请求触发器，则必须在调用的 `Content-Type` 标头中设置 `charset` 参数。 否则字符可能会被损坏，或者参数与有效负载的编码格式不匹配。 有关详细信息，请参阅[如何处理 `text/plain` 内容类型](logic-apps-content-type.md#text-plain)。

例如，使用正确的 `charset` 参数设置 `Content-Type` 标头时，HTTP 触发器会将传入内容转换为 UTF-8 格式：

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

如果将 `Content-Type` 标头设置为 `application/octet-stream`，则还可能收到非 UTF-8 字符。 有关详细信息，请参阅[如何处理 `application/octet-stream` 内容类型](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream)。

## <a name="base64-encode-content"></a>对内容使用 Base64 编码

在对内容使用 [base64 编码](workflow-definition-language-functions-reference.md#base64)之前，请确保已[将文本转换为 UTF-8 格式](#convert-payload-encoding)。 如果在将文本转换为 UTF-8 格式之前使用 base64 解码将内容转换为字符串，则字符可能会被损坏。

接下来，将所有 .NET 支持编码转换为其他 .NET 支持编码。 请参阅 [Azure Functions 代码示例](#azure-functions-version)或 [.NET 代码示例](#net-version)：

> [!TIP]
> 对于单租户逻辑应用，可以通过在本地运行转换函数，提高性能，减少延迟。

### <a name="azure-functions-version"></a>Azure Functions 版本

以下示例适用于 Azure Functions 版本 2： 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>.NET 版本

以下示例适用于 .NET Standard 和 Azure Functions 版本 2：

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

使用以上相同的概念，还可以[在工作流中发送非 Unicode 有效负载](#send-non-unicode-payload)。

## <a name="sample-payload-conversions"></a>示例有效负载转换

在此示例中，base64 编码的示例输入字符串是个人名称 H&eacute;lo&iuml;se，其中包含重音字符。

输入示例：

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

示例输出：

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>发送非 Unicode 有效负载

如果需要从工作流发送非 Unicode 有效负载，请反向执行[将有效负载转换为 UTF-8 格式](#convert-payload-encoding)的步骤。 让文本在系统中使用尽可能长的 UTF-8 格式。 接下来，使用相同的函数将 base64 编码 UTF-8 字符转换为所需编码字符。 然后，将 base64 解码应用于文本，并发送有效负载。

## <a name="convert-payloads-for-as2"></a>转换 AS2 的有效负载

你还可以将此解决方案用于 [AS2 v2 连接器](logic-apps-enterprise-integration-as2.md)中的非 Unicode 有效负载。 如果不将传递给 AS2 的有效负载转换为 UTF-8 格式，则可能遇到有效负载转译问题。 这些问题可能会导致合作伙伴之间的 MIC 哈希代码因误转译的字符而不一致。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Enterprise Integration Pack 对 Azure 逻辑应用中的平面文件进行编码或解码](logic-apps-enterprise-integration-flatfile.md)
