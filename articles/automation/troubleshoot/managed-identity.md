---
title: 排查 Azure 自动化托管标识问题（预览）
description: 本文介绍如何排查和解决将托管标识与自动化账户一起使用时遇到的问题。
services: automation
ms.subservice: ''
ms.date: 04/28/2021
ms.author: v-dturcaso
ms.topic: troubleshooting
ms.openlocfilehash: f42605b92093cf08a417690eae8324b9280e8ce6
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229882"
---
# <a name="troubleshoot-azure-automation-managed-identity-issues-preview"></a>排查 Azure 自动化托管标识问题（预览）

本文介绍将托管标识与自动化账户一起使用时可能会遇到的问题的解决方案。 有关将托管标识与自动化帐户一起使用的常规信息，请查看 [Azure 自动化帐户身份验证概述](../automation-security-overview.md#managed-identities-preview)。

## <a name="scenario-attempt-to-use-managed-identity-with-automation-account-fails"></a>方案：尝试将托管标识与自动化帐户一起使用时失败

### <a name="issue"></a>问题

尝试在自动化帐户中使用托管标识时，遇到如下错误：

```error
Connect-AzureRMAccount : An error occurred while sending the request. At line:2 char:1 + Connect-AzureRMAccount -Identity + 
CategoryInfo : CloseError: (:) [Connect-AzureRmAccount], HttpRequestException + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>原因

导致这种情况的最常见原因是，在尝试使用标识之前未启用该标识。 若要验证这一点，请在受影响的自动化帐户中运行以下 PowerShell runbook。

```powershell
resource= "?resource=https://management.azure.com/"
$url = $env:IDENTITY_ENDPOINT + $resource
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER)
$Headers.Add("Metadata", "True")

try
{
    $Response = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
}
catch
{
    $StatusCode = $_.Exception.Response.StatusCode.value__
    $stream = $_.Exception.Response.GetResponseStream()
    $reader = New-Object System.IO.StreamReader($stream)
    $responseBody = $reader.ReadToEnd()
    
    Write-Output "Request Failed with Status: $StatusCode, Message: $responseBody"
}
```

如果问题是你在尝试使用标识之前未启用该标识，则会看到如下所示的结果：

`Request Failed with Status: 400, Message: {"Message":"No managed identity was found for Automation account xxxxxxxxxxxx"}`

### <a name="resolution"></a>解决方法

必须为自动化帐户启用标识，然后才能使用托管标识服务。 请查看[为 Azure 自动化帐户启用托管标识（预览）](../enable-managed-identity-for-automation.md)

## <a name="next-steps"></a>后续步骤

如果本文未解决你的问题，请尝试通过以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 联系 [@AzureSupport](https://twitter.com/azuresupport)。 这是官方的 Microsoft Azure 帐户，它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。