---
title: Azure 证明故障排除指南
description: 有关排查常见问题的指南
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704310"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure 证明故障排除指南

Azure 证明中的错误处理是按照 [Microsoft REST API 准则](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)实施的。 Azure 证明 API 返回的错误响应包含 HTTP 状态代码，以及名称为“code”和“message”的名称/值对。 “code”的值是用户可读的，指示错误的类型。 “message”的值旨在辅助用户并提供错误详细信息。

如果你的问题在本文中没有得到解决，也可以在 [Azure 支持页](https://azure.microsoft.com/support/options/)上提交 Azure 支持请求。

下面是 Azure 证明返回的错误的一些示例：

## <a name="1-http401--unauthorized-exception"></a>1.HTTP –401：未授权异常

### <a name="http-status-code"></a>HTTP 状态代码
401

错误代码：未授权

场景示例
  - 在未为用户分配“证明读取者”角色的情况下发生了证明失败
  - 由于未为用户分配相应的角色，无法管理证明策略
  - 由于未为用户分配相应的角色，无法管理证明策略签名者

具有“读取者”角色的用户尝试在 PowerShell 中编辑证明策略 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**疑难解答步骤**

若要查看证明策略/策略签名者，Azure AD 用户需要“操作”权限：
- Microsoft.Attestation/attestationProviders/attestation/read

  可以通过“拥有者”（通配符权限）、“读取者”（通配符权限）或“证明读取者”（仅适用于 Azure 证明的特定权限）等角色将此权限分配给 AD 用户。

若要添加/删除策略签名者，或者要配置策略，Azure AD 用户需要以下“操作”权限：
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  可以通过“所有者”（通配符权限）、“参与者”（通配符权限）或“证明参与者”（仅适用于 Azure 证明的特定权限）等角色将这些权限分配给 AD 用户。

客户可以选择使用默认提供程序进行证明，或者使用自定义策略创建自己的提供程序。 若要将证明请求发送到自定义证明提供程序，用户需要“所有者”（通配符权限）、“读取者”（通配符权限）或“证明读取者”角色。 任何 Azure AD 用户都可以访问默认提供程序。

若要在 PowerShell 中验证角色，请执行以下操作：

a. 启动 PowerShell 并通过“Connect-AzAccount”cmdlet 登录到 Azure

b. 验证 Azure 角色分配设置


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  应看到与下面类似的内容：

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

## <a name="2-http--400-errors"></a>2.HTTP – 400 错误

### <a name="http-status-code"></a>HTTP 状态代码
400

请求可能出于不同的原因返回 400。 下面是 Azure 证明 API 返回的错误的一些示例：

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. 证明因策略评估错误而失败

证明策略包含授权规则和颁发规则。 enclave 证据是基于授权规则评估的。 颁发规则定义了要包含在证明令牌中的声明。 如果 enclave 证据中的声明不符合授权规则，则证明调用将返回策略评估错误。 

错误代码：PolicyEvaluationError

场景示例：enclave quote 中的声明与证明策略的授权规则不匹配

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

故障排除步骤：用户可以在配置 enclave 证据之前根据 SGX 证明策略评估该证据。

通过在“draftPolicyForAttestation”参数中提供策略文本，将请求发送到证明 API。 AttestSgxEnclave API 在证明调用期间将使用此策略文档，在使用证明策略之前，此文档可用于测试这些策略。 此字段存在时生成的证明令牌将不受保护。

请参阅[证明策略示例](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 证明因输入无效而失败

错误代码：InvalidParameter

场景示例：SGX 证明因输入无效而失败。 下面是一些示例错误消息：
- 由于 quote 附件中存在错误，指定的 quote 无效 
- 由于在其上生成 quote 的设备不符合 Azure 基线要求，指定的 quote 无效
- 由于 PCK 缓存服务提供的 TCBInfo 或 QEID 无效，指定的 quote 无效

**疑难解答步骤**

Microsoft Azure 证明支持对 Intel SDK 和 Open Enclave SDK 生成的 SGX quote 进行证明。

请参阅有关使用 Open Enclave SDK/Intel SDK 执行证明的[代码示例](/samples/browse/?expanded=azure&terms=attestation)

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. 上传策略/策略签名者时发生证书链无效错误

错误代码：InvalidParameter

场景示例：配置已签名的策略，或者添加/删除已使用无效证书链签名的策略签名者（例如，当根证书的“基本约束”扩展未设置为“使用者类型 = CA”时）

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

故障排除步骤：根证书必须标记为由 CA 颁发（X.509 基本约束），否则不会将其视为有效证书。 

确保根证书的“基本约束”扩展设置为“使用者类型 = CA”

否则，证书链将被视为无效。

请参阅[策略签名者](./policy-signer-examples.md)和[策略](./policy-examples.md)示例 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. 添加/删除策略签名者失败

错误代码：InvalidOperation

场景示例

用户上传不包含“maa-policyCertificate”声明的 JWS

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

用户未上传采用 JWS 格式的证书

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

故障排除步骤：若要添加/删除新的策略签名者证书，请使用包含名为“x-ms-policyCertificate”的声明的 RFC7519 JSON Web 令牌 (JWT)。 该声明的值是一个 RFC7517 JSON Web 密钥，其中包含要添加的证书。 必须使用与提供程序关联的任何有效策略签名者证书的私钥对 JWT 进行签名。 请参阅[策略签名者示例](./policy-signer-examples.md)。

### <a name="25-attestation-policy-configuration-failure"></a>2.5. 证明策略配置失败

错误代码：PolicyParsingError

场景示例：提供的策略采用了错误的语法（例如，缺少分号/有效的 JWT 策略）

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

错误代码：InvalidOperation

场景示例：提供的内容无效（例如，需要策略签名时上传了未签名的策略）

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

故障排除步骤：确保文本格式的策略采用 UTF-8 编码。

如果需要策略签名，则只能以 RFC7519 JSON Web 令牌 (JWT) 格式配置证明策略。 如果不需要策略签名，则可以采用文本或 JWT 格式配置策略。

若要以 JWT 格式配置策略，请使用包含名为“AttestationPolicy”的声明的 JWT。 该声明的值是策略文本的 Base64URL 编码版本。 如果为证明提供程序配置了策略签名者证书，则必须使用与该提供程序关联的任何有效策略签名者证书的私钥对 JWT 进行签名。 

若要以文本格式配置策略，请直接指定策略文本。

在 PowerShell 中，指定 JWT 作为 PolicyFormat，以便以 JWT 格式配置策略。 默认策略格式为 Text。

请参阅证明[策略示例](./policy-examples.md)以及[如何创作证明策略](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3.PowerShell 中的 Az.Attestation 安装问题

无法在 PowerShell 中安装 Az 或 Az.Attestation 模块

### <a name="error"></a>错误

警告: 无法解析包源 'https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package: 找不到与指定的搜索条件和模块名称匹配的项

### <a name="troubleshooting-steps"></a>疑难解答步骤

PowerShell 库已弃用传输层安全性 (TLS) 版本 1.0 和 1.1。 

建议使用 TLS 1.2 或更高版本。 

若要继续与 PowerShell 库进行交互，请在 Install-Module 命令之前运行以下命令

[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4.PowerShell 中的策略访问/配置问题

为用户分配了适当的角色。 但在通过 PowerShell 管理证明策略时遇到授权问题。

### <a name="error"></a>错误
对象 ID 为 &lt;object Id&gt; 的客户端未获授权，无权在“subcriptions/&lt;subscriptionId&gt;resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/&lt;role assignmentId&gt;”范围执行 Microsoft.Authorization/roleassignments/write 操作，或者范围无效。 如果最近已授予访问权限，请刷新凭据

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

例如 - Update-Module -Name Az.Attestation
