---
title: 为 Azure API 管理实例配置自定义域名
titleSuffix: Azure API Management
description: 本主题介绍了如何为 Azure API 管理实例配置自定义域名。
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: danlep
ms.openlocfilehash: 4cdd269c0acb7568695cbc63b47422ad9b42085b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598750"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>为 Azure API 管理实例配置自定义域名

创建 Azure API 管理服务实例时，Azure 将为它分配一个 `azure-api.net` 子域（例如 `apim-service-name.azure-api.net`）。 你还可以使用自己的自定义域名（例如 `contoso.com`）公开 API 管理终结点。 本教程演示了如何将现有的自定义 DNS 名称映射到 API 管理实例公开的终结点。

> [!IMPORTANT]
> API 管理仅接受[主机头](https://tools.ietf.org/html/rfc2616#section-14.23)值与以下项匹配的请求：
>
>* 默认域名
>* 已配置的任何自定义域名

> [!WARNING]
> 若要使用证书固定来提高应用程序的安全性，必须使用由你自己管理的自定义域名和证书，而不要使用默认证书。 固定默认证书就会硬性依赖于并非由你管理的证书属性，而我们不建议这样做。

## <a name="prerequisites"></a>先决条件

-   一个有效的 Azure 订阅。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
-   由你或你的组织拥有的自定义域名。 本主题不会提供有关如何购买自定义域名的说明。
-   [托管在 DNS 服务器上的 CNAME 记录](#dns-configuration)，该记录将自定义域名映射到 API 管理实例的默认域名。 本主题不会提供有关如何托管 CNAME 记录的说明。
-   一个包含公钥和私钥的有效证书 (.PFX)。 使用者或使用者可选名称 (SAN) 必须与域名匹配（这使得 API 管理实例可以通过 TLS 安全地公开 URL）。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 门户设置自定义域名

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 API 管理实例。
1. 选择“自定义域”。

    可以为许多终结点分配自定义域名。 当前有以下终结点可用：

    | 端点 | 默认 |
    | -------- | ----------- |
    | **网关** | 默认值为：`<apim-service-name>.azure-api.net`。 网关是唯一可用于“消耗”层中的配置的终结点。 |
    | 开发人员门户（旧版） | 默认值为：`<apim-service-name>.portal.azure-api.net` |
    | **开发人员门户** | 默认值为：`<apim-service-name>.developer.azure-api.net` |
    | **Management** | 默认值为：`<apim-service-name>.management.azure-api.net` |
    | SCM | 默认值为：`<apim-service-name>.scm.azure-api.net` |

    > [!NOTE]
    > 可以更新任何终结点。 通常，客户会更新“网关”（此 URL 用于调用通过 API 管理公开的 API）和“门户”（开发人员门户 URL） 。
    > 
    > 只有 API 管理实例所有者能够在内部使用“管理”和“SCM”终结点 。 很少会为这些终结点分配自定义域名。
    >
    > “高级”和“开发人员”层支持为“网关”终结点设置多个主机名称  。

1. 选择“+添加”，或选择要更新的现有终结点。
1. 在右侧窗口中，选择用于自定义域的终结点的“类型”。
1. 在“主机名”字段中，指定要使用的名称。 例如，`api.contoso.com`。
1. 在“证书”下，选择“密钥保管库”或“自定义”  。
    - **密钥保管库**
        - 单击“选择”。
        - 从下拉列表中选择“订阅”。
        - 从下拉列表中选择“密钥保管库”。
        - 加载证书后，从下拉列表中选择“证书”。
        - 单击“选择”。
    - **自定义**
        - 选择“证书文件”字段以选择并上传证书。
        - 上传有效的 .PFX 文件并提供其密码（如果该证书受密码保护）。
1. 配置网关终结点时，请根据需要选择或取消选择[其他选项](#clients-calling-with-server-name-indication-sni-header)，例如“协商客户端证书”或“默认 SSL 绑定” 。
1. 选择“更新”。

    > [!NOTE]
    > 除“消耗”层之外的所有层都支持类似于 `*.contoso.com` 的通配符域名。

    > [!TIP]
    > 建议[使用 Azure 密钥保管库管理证书](../key-vault/certificates/about-certificates.md)，并将证书设置为 `autorenew`。
    >
    > 如果使用 Azure Key Vault 来管理自定义域 TLS/SSL 证书，请确保该证书[作为证书](/rest/api/keyvault/createcertificate/createcertificate)而不是机密插入到 Key Vault 中 。
    >
    > 若要提取 TLS/SSL 证书，API 管理必须对包含该证书的 Azure Key Vault 具有“列表”和“获取机密”权限。 
    >
    >* 使用 Azure 门户时，所有必要的配置步骤将自动完成。 
    >* 使用命令行工具或管理 API 时，必须通过两个步骤手动授予这些权限：
    >    * 使用 API 管理实例上的“托管标识”页，确保已启用“托管标识”并记下该页上的主体 ID。 
    >    * 在包含证书的 Azure 密钥保管库上为此主体 ID 提供权限列表并获取机密权限。
    >
    > 如果证书设置为 `autorenew` 并且 API 管理层（除“开发人员”层以外的所有层）附带 SLA，API 管理将自动选取最新版本，而不会给服务造成任何中断。

1. 单击“应用”。

    > [!NOTE]
    > 分配证书的过程可能需要 15 分钟或更久，这取决于部署规模。 开发人员 SKU 可能会造成服务中断，而基本 SKU 和更高 SKU 则不会。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 配置

为自定义域名配置 DNS 时，可以执行以下任一步骤：

-   配置一条指向所配置自定义域名的终结点的 CNAME 记录，或
-   配置一条指向 API 管理网关 IP 地址的 A 记录。

虽然 CNAME 记录（即别名记录）和 A 记录都允许将域名与特定服务器或服务相关联，但两者的工作方式不同。 

### <a name="cname-or-alias-record"></a>CNAME 或别名记录
CNAME 记录将特定域（例如 `contoso.com` 或 www\.contoso.com）映射到规范域名。 创建后，CNAME 将为该域创建一个别名。 CNAME 条目将自动解析为自定义域服务的 IP 地址，因此，如果 IP 地址发生更改，你不必采取任何措施。

> [!NOTE]
> 某些域注册机构只允许在使用 CNAME 记录（例如 www\.contoso.com）时映射子域，而不允许在使用根名称（例如 contoso.com）时映射。 有关 CNAME 记录的详细信息，请参阅由注册机构提供的文档、[有关 CNAME 记录的 Wikipedia 条目](https://en.wikipedia.org/wiki/CNAME_record)或 [IETF 域名 - 实现和规范](https://tools.ietf.org/html/rfc1035)文档。

### <a name="a-record"></a>A 记录
A 记录将域（例如 `contoso.com` 或 www\.contoso.com）或通配符域（例如 \*.contoso.com）映射到 IP 地址。 由于 A 记录映射到静态 IP 地址，因此它无法自动解析 IP 地址的更改。 我们建议使用更稳定的 CNAME 记录，而不要使用 A 记录。

> [!NOTE]
> 尽管 API 管理实例 IP 地址是静态的，但在少数情况下它可能会更改。 选择 DNS 配置方法时，我们建议在配置自定义域时使用 CNAME 记录，因为在 IP 会发生更改的情况下，它比 A 记录更稳定。 有关详细信息，请参阅 [IP 文档文章](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)和 [API 管理常见问题解答](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-)。

## <a name="next-steps"></a>后续步骤

[升级和缩放你的服务](upgrade-and-scale.md)