---
title: 将证书内容转换为 base-64 - Azure HDInsight
description: 在 Azure HDInsight 中将服务主体证书内容转换为 base-64 编码的字符串格式
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944406"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>在 HDInsight 中将服务主体证书内容转换为 base-64 编码的字符串格式

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

你会收到一条错误消息，指出输入不是有效的 Base-64 字符串，因为它包含非 Base 64 字符、两个以上的填充字符，或者在填充字符中包含非空格字符。

## <a name="cause"></a>原因

使用 PowerShell 或 Azure 模板部署创建 Data Lake 作为主要或附加存储的群集时，所提供的用于访问 Data Lake 存储帐户的服务主体证书内容采用 base-64 格式。 将 pfx 证书内容转换为 base-64 编码的字符串不正确可能会导致此错误。

## <a name="resolution"></a>解决方法

获得 pfx 格式的服务主体证书后（有关服务主体创建步骤的示例，请参阅[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)），请使用以下 PowerShell 命令或 C# 代码片段将证书内容转换为 base-64 格式。

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]