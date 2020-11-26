---
title: Azure 证明故障排除指南
description: 常见问题故障排除指南
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 72d7a40c38f3629a70260f223074b456dff9ce38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182829"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure 认证故障排除指南

Azure 证明中的错误处理是按照 [Microsoft REST API 准则](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)实现的。 Azure 证明 Api 返回的错误响应包含 HTTP 状态代码和名称/值对，名称为 "code" 和 "message"。 "代码" 的值是用户可读的，并且是错误类型的指示器。 "消息" 的值旨在帮助用户并提供错误详细信息。

如果本文未解决你的问题，还可以在 [azure 支持页](https://azure.microsoft.com/support/options/)上提交 azure 支持请求。

下面是 Azure 证明返回的错误的一些示例：

## <a name="1-http401--unauthorized-exception"></a>1. HTTP –401：未经授权的异常

### <a name="http-status-code"></a>HTTP 状态代码
401

**错误代码** 未经许可

**方案示例**
  - 如果用户未分配有证明读者角色，则证明失败
  - 无法管理证明策略，因为未向用户分配适当的角色
  - 由于未向用户分配适当的角色，因此无法管理证明策略签名者

具有读取者角色的用户尝试在 PowerShell 中编辑证明策略 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**疑难解答步骤**

若要查看证明策略/策略签名者，Azure AD 用户需要 "操作" 权限：
- Microsoft.Attestation/attestationProviders/attestation/read

  此权限可通过角色分配给 AD 用户，例如 "所有者" (通配符权限) 或 "读取者" (通配符权限) 或 "证明读者" (仅适用于 Azure 证明) 的特定权限。

若要添加/删除策略签名者或配置策略，Azure AD 用户需要以下 "操作" 权限：
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  这些权限可通过角色分配给 AD 用户，例如 "所有者" (通配符权限) ，"参与者" (通配符权限) 或 "证明参与者" (仅) 的 Azure 证明特定权限。

客户可以选择使用默认提供程序进行证明，或者使用自定义策略创建自己的提供程序。 若要将证明请求发送到自定义证明提供程序，"Owner" (通配符权限) 或 "Reader" (通配符权限) 或 "证明读者" 角色是用户所必需的。 任何 Azure AD 用户都可以访问默认提供程序。

若要验证 PowerShell 中的角色，请运行以下内容：

a. 启动 PowerShell 并通过 "AzAccount" cmdlet 登录到 Azure

b. 验证你的 Azure 角色分配设置


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  你应看到与下面类似的内容：

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. 如果在列表中找不到相应的角色分配，请按照[此处](../role-based-access-control/role-assignments-powershell.md)的说明进行操作

## <a name="2-http--400-errors"></a>2. HTTP –400错误

### <a name="http-status-code"></a>HTTP 状态代码
400

请求可能返回400的原因有多种。 下面是 Azure 证明 Api 返回的错误的一些示例：

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. 由于策略评估错误，证明失败

证明策略包括授权规则和颁发规则。 根据授权规则评估 Enclave 证据。 颁发规则定义要包括在证明令牌中的声明。 如果 enclave 证据中的声明与授权规则不符，则证明调用将返回策略评估错误。 

**错误代码** PolicyEvaluationError

**方案示例** Enclave 引号中的声明与证明策略的授权规则不匹配时

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**故障排除步骤** 在配置相同的之前，用户可以针对 SGX 证明策略评估 enclave 证据。

通过在 "draftPolicyForAttestation" 参数中提供策略文本，将请求发送到证明 API。 在证明调用期间，AttestSgxEnclave API 将使用此策略文档，这可用于在证明策略使用之前对其进行测试。 存在此字段时生成的证明令牌将不受保护。

请参阅 [证明策略示例](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 由于输入无效，证明失败

**错误代码** InvalidParameter

**方案示例** 由于输入无效，出现 SGX 证明失败。 下面是一些错误消息示例：
- 由于报价宣传资料中的错误，指定的报价无效 
- 指定的引号无效，因为在其上生成报价单的设备不满足 Azure 基线要求
- 指定的引号无效，因为 PCK 缓存服务提供的 TCBInfo 或 QEID 无效

**疑难解答步骤**

Microsoft Azure 证明支持通过 Intel SDK 和 Open Enclave SDK 生成的 SGX 报价的证明。

请参阅使用开放式 Enclave SDK/Intel SDK 执行证明的[代码示例](/samples/browse/?expanded=azure&terms=attestation)

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. 上传策略/策略签名者时证书链错误无效

**错误代码** InvalidParameter

**方案示例** 配置签名策略或添加/删除策略签名程序，该签名使用无效的证书链进行签名 (例如，根证书的基本约束扩展未设置为 "使用者类型 = CA") 

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**故障排除步骤** 根证书必须标记为 CA 颁发的证书， (x.509 基本约束) ，否则不会将其视为有效证书。 

确保根证书的基本约束扩展已设置为指示使用者类型 = CA

否则，证书链被视为无效。

请参阅 [策略签名者](./policy-signer-examples.md) 和 [策略](./policy-examples.md) 示例 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. 添加/删除策略签名者失败

**错误代码** InvalidOperation

**方案示例**

用户上传 JWS 时，如果不包含 "maa-policyCertificate" 声明

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

如果用户未上传 JWS 格式的证书

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**故障排除步骤** 若要添加/删除新策略签名者证书，请使用名为 "policyCertificate" 的声明 (JWT Web 令牌 JWT) 。 声明的值是 RFC7517 的 JSON Web 密钥，其中包含要添加的证书。 JWT 必须用与该提供程序关联的任何有效策略签名者证书的私钥进行签名。 请参阅 [策略签名者示例](./policy-signer-examples.md)。

### <a name="25-attestation-policy-configuration-failure"></a>2.5. 证明策略配置失败

**错误代码** PolicyParsingError

**方案示例** 提供的策略的语法不正确 (例如，缺少分号) /valid JWT 策略) 

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**错误代码** InvalidOperation

**方案示例** 提供的内容无效 (例如，在需要策略签名时上载策略/未签名策略) 

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**故障排除步骤** 确保文本格式的策略是 UTF-8 编码的。

如果需要策略签名，则必须仅在 RFC7519 JSON Web 令牌 (JWT) 格式中配置证明策略。 如果不需要策略签名，则可以采用文本或 JWT 格式配置策略。

若要以 JWT 格式配置策略，请使用具有名为 "AttestationPolicy" 的声明的 JWT。 声明的值为策略文本的 Base64URL 编码版本。 如果证明提供程序配置有策略签名者证书，则必须用与提供程序关联的任何有效策略签名者证书的私钥对 JWT 进行签名。 

若要以文本格式配置策略，请直接指定策略文本。

在 PowerShell 中，将 PolicyFormat 指定为 JWT，以 JWT 格式配置策略。 默认策略格式为 "文本"。

请参阅证明 [策略示例](./policy-examples.md) 和 [如何编写证明策略](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Az。 PowerShell 中的证明安装问题

无法在 PowerShell 中安装 Az 或 Az 模块

### <a name="error"></a>错误

警告：无法解析包源 " https://www.powershellgallery.com/api/v2 " PackageManagement\Install-Package：找不到与指定的搜索条件和模块名称匹配的匹配项

### <a name="troubleshooting-steps"></a>疑难解答步骤

PowerShell 库已弃用 (TLS) 版本1.0 和1.1 的传输层安全性。 

建议使用 TLS 1.2 或更高版本。 

若要继续与 PowerShell 库进行交互，请在 Install-Module 命令之前运行以下命令

**[ServicePointManager]：： SecurityProtocol = [SecurityProtocolType]：： Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. PowerShell 中的策略访问/配置问题

用户分配了适当的角色。 但在通过 PowerShell 管理证明策略时，面临的授权问题。

### <a name="error"></a>错误
具有对象 id 的客户端无权对 <object Id>  作用域 "开放式/resourcegroups/secure_enclave_poc/providers/microsoft.authorization/roleassignments/" 执行操作/roleassignments/写入， <subscriptionId> <role assignmentId> 或者范围无效。 如果最近授予了访问权限，请刷新凭据

### <a name="troubleshooting-steps"></a>疑难解答步骤

支持证明操作所需的 Az 模块的最低版本如下： 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

运行以下命令，验证所有 Az 模块的已安装版本 

```powershell
Get-InstalledModule 
```

如果版本与最低要求不匹配，请运行 Update-Module 命令

例如 Update-Module-Name Az. 证明