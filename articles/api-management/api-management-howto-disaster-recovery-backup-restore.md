---
title: 使用 API 管理中的备份和还原实现灾难恢复
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中使用备份和还原执行灾难恢复。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 10/03/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b356d18c1a0c6a29d4fce142fc05e449f08f70d2
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419138"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>如何使用 Azure API 管理中的服务备份和还原实现灾难恢复

通过 Azure API 管理来发布和管理 API，即可充分利用容错和基础结构功能，否则需手动设计、实现和管理这些功能。 Azure 平台通过花费少量成本消除大量潜在故障。

若要从影响托管着 API 管理服务的区域的可用性问题中恢复，应随时准备好在另一区域中重建服务。 根据恢复时间目标，你可能希望在一个或多个区域中保留备用服务。 你还可以根据自己的恢复点目标，尝试将其配置和内容与活动服务保持同步。 服务备份和还原功能为实现灾难恢复策略提供必要的构建基块。

备份和还原操作还可用于在操作环境（例如，开发环境和过渡环境）之间复制 API 管理服务配置。 请注意，运行时数据（如用户和订阅）也将被复制，这可能并不总是理想的。

本指南介绍如何自动执行备份和还原操作，以及如何确保 Azure 资源管理器成功验证备份和还原请求。

> [!IMPORTANT]
> 还原操作不会更改目标服务的自定义主机名配置。 我们建议对活动服务和备用服务使用相同的自定义主机名和 TLS 证书，以便在还原操作完成后，可以通过简单的 DNS CNAME 更改将流量重定向到备用实例。
>
> 备份操作不会捕获 Azure 门户的“分析”边栏选项卡上显示的报告中使用的预聚合日志数据。

> [!WARNING]
> 每个备份都会在 30 天后过期。 如果在 30 天有效期到期后尝试还原备份，还原会失败并显示 `Cannot restore: backup expired` 消息。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>对 Azure 资源管理器请求进行身份验证

> [!IMPORTANT]
> 用于还原和备份的 REST API 使用 Azure 资源管理器，并且具有与用于管理 API 管理实体的 REST API 不同的身份验证机制。 本部分中的步骤介绍如何对 Azure 资源管理器请求进行身份验证。 有关详细信息，请参阅[对 Azure 资源管理器请求进行身份验证](/rest/api/azure)。

使用 Azure 资源管理器对资源所进行的所有任务都必须使用以下步骤通过 Azure Active Directory 进行身份验证：

-   向 Azure Active Directory 租户添加应用程序。
-   为添加的应用程序设置权限。
-   获取用于对 Azure 资源管理器的请求进行身份验证的令牌。

### <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 使用包含 API 管理服务实例的订阅，导航到[“Azure 门户”-“应用注册”](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)以在 Active Directory 域服务注册应用。
    > [!NOTE]
    > 如果 Azure Active Directory 默认目录对帐户不可见，请联系 Azure 订阅的管理员，要求他们向你的帐户授予必要的权限。
1. 选择“+新建注册”。
1. 在“注册应用程序”页上，将值设置如下：
    
    * 将“名称”设置为一个有意义的名称。
    * 将“支持的帐户类型”设置为“仅限此组织目录中的帐户”。  
    * 在“重定向 URI”中输入占位符 URL，例如 `https://resources`。 这是必填字段，但稍后不会使用该值。 
    * 选择“注册”。

### <a name="add-permissions"></a>添加权限

1. 创建应用程序后，请选择“API 权限” > “+ 添加权限”。 
1. 选择“Microsoft API”。
1. 选择“Azure 服务管理”。

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="显示如何添加应用权限的屏幕截图。"::: 

1. 单击新添加应用程序旁边的“委派的权限”，选中“以组织用户身份访问 Azure 服务管理（预览版）”复选框。 

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="显示如何添加委派的应用权限的屏幕截图。":::

1. 选择“添加权限”。

### <a name="configure-your-app"></a>配置应用程序

在调用可生成备份与还原的 API 之前，你需获取令牌。 以下示例使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 包来检索令牌。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

根据以下说明替换 `{tenant id}`、`{application id}` 和 `{redirect uri}`。

1. 将 `{tenant id}` 替换为已创建的 Azure Active Directory 应用程序的租户 ID。 可通过单击“应用注册” -> “终结点”访问此 ID。  

    ![终结点][api-management-endpoint]

2. 将 `{application id}` 替换为通过导航到“设置”  页面获得的值。
3. 将 `{redirect uri}` 替换为 Azure Active Directory 应用程序“重定向 URI”选项卡上的值。 

    指定这些值后，代码示例应返回类似于以下示例的令牌：

    ![令牌][api-management-arm-token]

    > [!NOTE]
    > 该令牌可能在一段时间后过期。 再次执行示例代码以生成新令牌。

## <a name="accessing-azure-storage"></a>访问 Azure 存储

API 管理使用你为备份和还原操作指定的 Azure 存储帐户。 运行备份或还原操作时，需要配置对存储帐户的访问权限。 API 管理支持两种存储访问机制：Azure 存储访问密钥（默认）或 API 管理托管标识。

### <a name="configure-storage-account-access-key"></a>配置存储帐户访问密钥

有关步骤，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md?tabs=azure-portal)。

### <a name="configure-api-management-managed-identity"></a>配置 API 管理托管标识

> [!NOTE]
> 若要在备份和还原期间使用 API 管理托管标识进行存储操作，需有 API 管理 REST API 版本 `2021-04-01-preview` 或更高版本。

1. 在 API 管理实例中[为 API 管理启用系统分配的托管标识或用户分配的托管标识](api-management-howto-use-managed-service-identity.md)。

    * 如果启用用户分配的托管标识，请记下该标识的“客户端 ID”。
    * 如果要备份和还原到不同的 API 管理实例，请在源实例和目标实例中都启用托管标识。
1. 为该标识分配“存储 Blob 数据参与者”角色，该角色的范围限定为用于备份和还原的存储帐户。 若要分配角色，请使用 [Azure 门户](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)或其他 Azure 工具。

## <a name="calling-the-backup-and-restore-operations"></a>调用备份和还原操作

REST API 为 [Api 管理服务 - 备份](/rest/api/apimanagement/2020-12-01/api-management-service/backup)和 [Api 管理服务 - 还原](/rest/api/apimanagement/2020-12-01/api-management-service/restore)。

> [!NOTE]
> 也可分别使用 PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) 和 [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) 命令，执行备份和还原操作。

在调用以下部分中所述的“备份和还原”操作之前，为 REST 调用设置授权请求标头。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>备份 API 管理服务

若要备份 API 管理服务问题，请发送以下 HTTP 请求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

其中：

-   `subscriptionId` - 订阅的 ID，该订阅包含的 API 管理服务是你尝试备份的
-   `resourceGroupName` - Azure API 管理服务的资源组名称
-   `serviceName` - 正在创建其备份的 API 管理服务的名称，在创建时指定
-   `api-version` - 有效的 REST API 版本，例如 `2020-12-01` 或 `2021-04-01-preview`。

在请求正文中，指定目标存储帐户名称、Blob 容器名称、备份名称和存储访问类型。 如果存储容器不存在，备份操作将创建存储容器。

#### <a name="access-using-storage-access-key"></a>使用存储访问密钥进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>使用托管标识进行访问

> [!NOTE]
> 若要在备份和还原期间使用 API 管理托管标识进行存储操作，需有 API 管理 REST API 版本 `2021-04-01-preview` 或更高版本。

使用系统分配的托管标识进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

使用用户分配的托管标识进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```


将 `Content-Type` 请求标头的值设置为 `application/json`。

备份是长时间运行的操作，可能需要数分钟才能完成。 如果请求已成功且备份过程已开始，则会收到带有 `Location` 标头的 `202 Accepted` 响应状态代码。 向 `Location` 头中的 URL 发出 `GET` 请求以查明操作状态。 当备份正在进行时，你将继续收到 `202 Accepted` 状态代码。 响应代码 `200 OK` 指示备份操作成功完成。

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>还原 API 管理服务

若要从之前创建的备份还原 API 管理服务，请发出以下 HTTP 请求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

其中：

-   `subscriptionId` - 订阅 ID，该订阅包含的 API 管理服务是需要将备份还原到其中的
-   `resourceGroupName` - 资源组的名称，该资源组包含的 Azure API 管理服务是需要将备份还原到其中的
-   `serviceName` - 要将备份还原到其中的 API 管理服务的名称，在创建时指定
-   `api-version` - 有效的 REST API 版本，例如 `2020-12-01` 或 `2021-04-01-preview`

在请求正文中，指定现有存储帐户名称、Blob 容器名称、备份名称和存储访问类型。 

#### <a name="access-using-storage-access-key"></a>使用存储访问密钥进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>使用托管标识进行访问

> [!NOTE]
> 若要在备份和还原期间使用 API 管理托管标识进行存储操作，需有 API 管理 REST API 版本 `2021-04-01-preview` 或更高版本。

使用系统分配的托管标识进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

使用用户分配的托管标识进行访问

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```

将 `Content-Type` 请求标头的值设置为 `application/json`。

还原是长时间运行的操作，可能需要长达 30 分钟或更长时间才能完成。 如果请求已成功且还原过程已开始，则会收到带有 `Location` 标头的 `202 Accepted` 响应状态代码。 向 `Location` 标头中的 URL 发出“GET”请求以查明操作状态。 当还原正在进行时，你将继续收到 `202 Accepted` 状态代码。 响应代码 `200 OK` 指示还原操作成功完成。

> [!IMPORTANT]
> 要还原到的服务的 **SKU** 必须与正在还原的已备份服务的 SKU **匹配**。
>
> 还原操作正在进行时对服务配置（例如 API、策略、开发人员门户外观）所做的 **更改** **可能会被覆盖**。

## <a name="constraints-when-making-backup-or-restore-request"></a>发出备份请求或还原请求时的限制

-   当备份正在进行时，请 **避免在服务中进行管理更改**，例如 SKU 升级或降级、域名更改等。
-   从创建时开始，**备份还原仅保证 30 天**。
-   在正在进行备份操作时对服务配置（例如，API、策略和开发人员门户外观）所做的更改可能不会包含在备份中，将会丢失 。

-   不应在 Azure 存储帐户中的 Blob 服务上启用[跨域资源共享 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。
-   要还原到的服务的 **SKU** 必须与正在还原的已备份服务的 SKU **匹配**。

## <a name="storage-networking-constraints"></a>存储网络约束

### <a name="access-using-storage-access-key"></a>使用存储访问密钥进行访问

如果存储帐户启用了[防火墙][azure-storage-ip-firewall]并使用存储密钥进行访问，则客户必须允许其存储帐户上的 [Azure API 管理控制平面 IP 地址][control-plane-ip-address]集，这样才能正常进行备份或还原 。 存储帐户可以位于除 API 管理服务所在区域之外的任意 Azure 区域中。 例如，如果 API 管理服务位于美国西部，Azure 存储帐户就可以位于美国西部 2，并且客户需要在防火墙中打开控制平面 IP 13.64.39.16（美国西部的 API 管理控制平面 IP）。 这是因为对 Azure 存储的请求不会从同一 Azure 区域中的计算（Azure API 管理控制平面）进行 SNAT 转换后发送到公共 IP。 跨区域存储请求将在经过 SNAT 转换后发送到公共 IP 地址。

### <a name="access-using-managed-identity"></a>使用托管标识进行访问

如果使用 API 管理的系统分配的托管标识来访问启用了防火墙的存储帐户，请确保该存储帐户[向受信任的 Azure 服务授予访问权限](../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-to-trusted-azure-services)。

## <a name="what-is-not-backed-up"></a>不备份的内容
-   用于创建分析报表的 **用法数据** **不包括** 在备份中。 使用 [Azure API 管理 REST API][azure api management rest api] 定期检索分析报表以保证安全。
-   [自定义域 TLS/SSL](configure-custom-domain.md) 证书。
-   [自定义 CA 证书](api-management-howto-ca-certificates.md)，包括由客户上传的中间证书或根证书。
-   [虚拟网络](api-management-using-with-vnet.md)集成设置。
-   [托管标识](api-management-howto-use-managed-service-identity.md)配置。
-   [Azure Monitor 诊断](api-management-howto-use-azure-monitor.md)配置。
-   [协议和密码](api-management-howto-manage-protocols-ciphers.md)设置。
-   [开发人员门户](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management)内容。

执行服务备份的频率将影响恢复点目标。 为了最大程度减少它，建议实施定期备份，以及在对 API 管理服务进行更改后执行按需备份。

## <a name="next-steps"></a>后续步骤

请查看以下相关资源来了解备份/还原过程：

-   [使用逻辑应用自动执行 API 管理备份和还原](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
- [如何跨区域移动 Azure API 管理](api-management-howto-migrate.md)

API 管理“高级”层还支持[区域冗余](zone-redundancy.md)，该功能可为特定 Azure 区域（位置）中的服务实例提供复原能力和高可用性。

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ip-addresses
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
