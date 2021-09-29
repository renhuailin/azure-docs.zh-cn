---
title: 测试 Azure 市场的 Azure 虚拟机映像
description: 在 Azure 市场中测试和提交 Azure 虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: ebolton-cyber
ms.author: edewebolton
ms.date: 03/10/2021
ms.openlocfilehash: 876f4b42c77caba27ed94b42d784004b0bfd32ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598149"
---
# <a name="test-a-virtual-machine-image"></a>测试虚拟机映像

本主题介绍 Azure 市场中用于部署的虚拟机 (VM) 映像的测试步骤。

## <a name="deploy-an-azure-vm"></a>部署 Azure VM

若要从共享映像库映像部署 VM，请执行以下操作：

1. 导航到共享映像库映像版本
1. 单击“创建 VM”
1. 提供虚拟机名称并选择 VM 大小
1. 单击“查看 + 创建”。 通过验证后，单击“创建”

> [!NOTE]
> 如果需要从 VHD 文件创建 VM，请按照以下文章中的说明操作：[准备 Azure 资源管理器模板](#connect-the-certification-tool-to-a-vm-image)或[使用 PowerShell 部署 Azure VM](#how-to-use-powershell-to-consume-the-self-test-api)。

本文介绍如何在商业市场中测试和提交虚拟机 (VM) 映像，以确保它满足最新的 Azure 市场发布要求。

在提交 VM 产品/服务之前，请先完成以下步骤：

- 使用通用化映像部署 Azure VM；请参阅[使用已批准的基础映像创建虚拟机](azure-vm-create-using-approved-base.md)或[使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。
- 运行验证。

## <a name="run-validations"></a>运行验证

可以通过两种方式对已部署的映像运行验证。

### <a name="use-certification-test-tool-for-azure-certified"></a>使用 Azure 认证的认证测试工具

#### <a name="download-and-run-the-certification-test-tool"></a>下载并运行认证测试工具

“Azure 认证”的认证测试工具在本地 Windows 计算机上运行，但可以测试基于 Azure 的 Windows 或 Linux VM。 它可证明用户 VM 映像可与 Microsoft Azure 一起使用，并且已满足有关准备 VHD 的指南和要求。

1. 下载并安装最新的[“Azure 认证”的认证测试工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 打开认证工具，然后选择“启动新测试”。
3. 在“测试信息”屏幕中，为测试运行输入测试名称。
4. 为 VM 选择平台，可以是“Windows Server”或“Linux” 。 所选的平台会影响剩余的选项。
5. 如果 VM 使用此数据库服务，请选中“针对 Azure SQL 数据库进行测试”复选框。

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>将认证工具连接到 VM 映像

1. 选择“SSH 身份验证”模式：密码身份验证或密钥文件身份验证。
2. 如果使用基于密码的身份验证，请输入“VM DNS 名称”、“用户名”和“密码”的值  。 还可以更改默认的“SSH 端口”号。

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="显示 VM 测试信息的选择。":::

3. 如果使用基于密钥文件的身份验证，请输入“VM DNS 名称”、“用户名”和“私钥”位置的值。   还可以包含通行短语，或更改默认的“SSH 端口”号 。
4. 输入完全限定的VM DNS 名称（例如 MyVMName.Cloudapp.net）。
5. 输入 **用户名** 和 **密码**。

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="显示 VM 用户名和密码的选择。":::

6. 选择“**下一页**”。

#### <a name="run-a-certification-test"></a>运行认证测试

在认证工具中提供 VM 映像的参数值后，选择“测试连接”以创建与 VM 的有效连接。 验证连接后，选择“下一步”启动测试。 测试完成后，结果将显示在表中。 “状态”列显示每个测试（通过/失败/警告）。 如有任何测试失败，则不会认证该映像。 在这种情况下，请查看要求和失败消息，根据建议进行更改，然后再次运行测试。

自动测试完成后，请在“调查表”屏幕的两个选项卡“常规评估”和“内核自定义”上提供有关 VM 映像的其他信息，然后选择“下一步”   。

在最后一个屏幕中可以提供更多信息，例如 Linux VM 映像的 SSH 访问信息，以及在查找异常时对任何失败评估的说明。

最后，请选择“生成报告”下载已执行的测试案例的测试结果和日志文件，以及调查表的答案。
> [!Note]
> 有少数发布者存在这样的情况：由于其 VM 上安装了诸如防火墙之类的软件，因此需要锁定 VM。 对于这种情况，请在此处下载[已认证的测试工具](https://aka.ms/AzureCertificationTestTool)，并通过合作伙伴中心的[支持](https://aka.ms/marketplacepublishersupport)菜单提交报告。

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>如何通过 PowerShell 使用 Self-Test API

### <a name="on-linux-os"></a>在 Linux OS 上

在 PowerShell 中调用该 API：

1. 使用 Invoke-WebRequest 命令调用该 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和屏幕截图所示。
3. 采用 JSON 格式指定正文参数。

以下示例显示了对 API 的 PowerShell 调用：

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>下面是在 PowerShell 中调用该 API 的示例：

[![在 PowerShell 中调用 API 的示例屏幕。](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>此示例屏幕显示了 `$res.Content`，以及 JSON 格式的测试结果的详细信息：

[![在 PowerShell 中调用 API 的屏幕示例，其中包含测试结果的详细信息。](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>下面是在联机 JSON 查看器（如 [Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON 查看器](https://jsonformatter.org/json-viewer)）中查看的 JSON 测试结果示例。

![联机 JSON 查看器中的更多测试结果。](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>在 Windows OS 上

在 PowerShell 中调用该 API：

1. 使用 Invoke-WebRequest 命令调用该 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和示例屏幕中所示。
3. 创建采用 JSON 格式的正文参数。

此代码示例显示对 API 的 PowerShell 调用：

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

以下示例屏幕显示了在 PowerShell 中调用 API 的示例：

**使用 SSH 密钥**：

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="使用 SSH 密钥在 PowerShell 中调用 API。":::

**使用密码**：

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="使用密码在 PowerShell 中调用 API。":::

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>此屏幕显示了 `$res.Content`，以及 JSON 格式的测试结果的详细信息：

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="采用 JSON 格式的测试结果的详细信息。":::

<br>下面是在联机 JSON 查看器（如 [Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON 查看器](https://jsonformatter.org/json-viewer)）中查看的测试结果示例。

![联机 JSON 查看器中的测试结果。](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>如何在 Linux OS 上通过 CURL 使用 Self-Test API

在此示例中，将使用 curl 对 Azure Active Directory 和自托管 VM 发出 POST API 调用。

1. 请求一个用于对自托管 VM 进行身份验证的 Azure AD 令牌

   确保使用正确的值替换 curl 请求中的相应值。

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   下面是来自请求的响应示例：

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. 提交针对自测试 VM 的请求

   确保将持有者令牌和参数替换为正确的值。

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   来自自测试 VM API 调用的示例响应

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>后续步骤

- 登录[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2166002)。
