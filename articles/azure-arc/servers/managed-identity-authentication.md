---
title: 针对已启用 Arc 的服务器对 Azure 资源进行身份验证
description: 本文介绍了对于已启用 Arc 的服务器的 Azure 实例元数据服务支持，以及如何使用机密对 Azure 资源和本地资源进行身份验证。
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 76f7174792f751322545b1d30bb51476c5339e26
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389903"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>针对已启用 Arc 的服务器对 Azure 资源进行身份验证

直接在已启用 Azure Arc 的服务器上运行的应用程序或进程可以利用托管标识来访问支持基于 Azure Active Directory 的身份验证的其他 Azure 资源。 应用程序可以获取代表其标识（由系统分配用于已启用 Arc 的服务器）的[访问令牌](../../active-directory/develop/developer-glossary.md#access-token)，并使用该令牌作为“持有者”令牌在其他服务中验证自己的身份。

有关托管标识的详细介绍以及系统分配的标识和用户分配的标识之间的区别，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)文档。

本文介绍了服务器如何使用系统分配的托管标识来访问 Azure [Key Vault](../../key-vault/general/overview.md)。 作为引导，Key Vault 随后可让客户端应用程序使用机密访问未受 Azure Active Directory (AD) 保护的资源。 例如，IIS web 服务器使用的 TLS/SSL 证书可以存储在 Azure Key Vault 中，并安全地将证书部署到 Azure 外部的 Windows 或 Linux 服务器。

## <a name="security-overview"></a>安全概述

将服务器加入已启用 Azure Arc 的服务器时，需要执行几个操作来配置托管标识的使用，类似于对 Azure VM 执行的操作：

- Azure 资源管理器收到请求，要求在已启用 Arc 的服务器上启用系统分配的托管标识。

- Azure 资源管理器在 Azure AD 中创建与服务器标识相对应的服务主体。 服务主体在此订阅信任的 Azure AD 租户中创建。

- Azure 资源管理器通过使用服务主体客户端 ID 和证书更新用于 [Windows](../../virtual-machines/windows/instance-metadata-service.md) 或 [Linux](../../virtual-machines/linux/instance-metadata-service.md) 的 Azure 实例元数据服务 (IMDS) 标识终结点来配置服务器上的标识。 终结点是一个 REST 终结点，只能使用众所周知且不可路由的 IP 地址从服务器内部进行访问。 此服务提供一部分有关已启用 Arc 的服务器的元数据信息，以帮助对其进行管理和配置。

在已启用 Windows Arc 的服务器上使用以下变量来配置启用了托管标识的服务器的环境：

- IMDS_ENDPOINT：已启用 Arc 的服务器的 IMDS 终结点 IP 地址 `http://localhost:40342`。

- **IDENTITY_ENDPOINT**：与服务的托管标识 `http://localhost:40342/metadata/identity/oauth2/token` 对应的 localhost 终结点。

在服务器上运行的代码可以从只能从该服务器中访问的 Azure 实例元数据服务终结点请求令牌。

系统环境变量 IDENTITY_ENDPOINT 用于发现应用程序的标识终结点。  应用程序应尝试检索 IDENTITY_ENDPOINT 和 IMDS_ENDPOINT 值并使用这些值。 允许具有任何访问级别的应用程序向终结点发出请求。 像平常一样对元数据响进行处理，并将其提供给计算机上的任何进程。 但是，当发出的请求会公开令牌时，我们要求客户端提供一个机密来证明他们能够访问仅可供更高特权用户访问的数据。

## <a name="prerequisites"></a>先决条件

- 具备托管标识相关知识。
- 使用已启用 Arc 的服务器连接和注册的服务器。
- 你是订阅或资源组中[所有者组](../../role-based-access-control/built-in-roles.md#owner)的成员以便执行所需的资源创建和角色管理步骤。
- 用于存储和检索凭据的 Azure Key Vault。 并将 Azure Arc 标识访问权限分配给 KeyVault。

    - 如果尚未创建 Key Vault，请参阅[创建 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)。
    - 若要通过服务器使用的托管标识配置访问权限，请参阅[为 Linux 授予访问权限](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access)或[为 Windows 授予访问权限](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access)。 对于第 5 步，你将输入已启用 Arc 的服务器的名称。 若要使用 PowerShell 完成此操作，请参阅[使用 Powershell 分配访问策略](../../key-vault/general/assign-access-policy-powershell.md)。

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 获取访问令牌

获取并使用系统分配的托管标识对 Azure 资源进行身份验证的方法与对 Azure VM 进行身份验证的方法类似。

对于已启用 Arc 的 Windows 服务器，使用 PowerShell 时，将调用 Web 请求以从特定端口的本地主机获取令牌。 使用 IP 地址或环境变量 IDENTITY_ENDPOINT 来指定请求。

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

以下响应是返回结果的示例：

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="使用 PowerShell 成功检索访问令牌。":::

对于已启用 Arc 的 Linux 服务器，使用 Bash 时，将调用 Web 请求以从特定端口的本地主机获取令牌。 使用 IP 地址或环境变量 IDENTITY_ENDPOINT 来指定以下请求。 若要完成此步骤，需要 SSH 客户端。

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

以下响应是返回结果的示例：

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="使用 Bash 成功检索访问令牌。":::

响应包括访问 Azure 中任意资源所需的访问令牌。 若要完成对 Azure Key Vault 进行身份验证的配置，请参阅[访问 Windows 的 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) 或[访问 Linux 的 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure Key Vault 的详细信息，请参阅 [Azure Key Vault 概述](../../key-vault/general/overview.md)。

- 了解如何[使用 PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 或使用 [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md) 向资源分配托管标识访问权限。