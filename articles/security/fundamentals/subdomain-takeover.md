---
title: 使用 Azure DNS 别名记录和 Azure 应用服务自定义域验证来防止子域接管
description: 了解如何避免子域接管造成的常见严重威胁
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: 7c09a7f6c6a313852fc6212c6190a584ba5f67bd
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409886"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>阻止无关联的 DNS 项并避免子域接管

本文介绍了子域接管造成的常见安全威胁，以及可采取的缓解措施。


## <a name="what-is-subdomain-takeover"></a>什么是子域接管？

子域接管是定期创建和删除大量资源的组织会遇到的一种常见严重威胁。 当你有 [DNS 记录](../../dns/dns-zones-records.md#dns-records)指向已取消预配的 Azure 资源时，可能会发生子域接管。 这类 DNS 记录也称为“无关联的 DNS”项。 CNAME 记录特别容易受到此威胁的攻击。 子域接管使恶意操作者能够将专用于某组织的域的流量重定向到一个执行恶意活动的站点。

出现子域接管的常见情况：

1. **创建：**

    1. 你使用完全限定的域名 (FQDN) `app-contogreat-dev-001.azurewebsites.net` 预配 Azure 资源。

    1. 你使用将流量路由到你的 Azure 资源的子域 `greatapp.contoso.com` 在 DNS 区域中分配 CNAME 记录。

1. **取消预配：**

    1. 不再需要 Azure 资源时将其取消预配或删除。 
    
        此时，应从 DNS 区域删除 CNAME 记录 `greatapp.contoso.com`。 如果未删除 CNAME 记录，则该记录将播发为活动域，但不会将流量路由到活动的 Azure 资源。 这就是“无关联的”DNS 记录的定义。

    1. 无关联的子域 `greatapp.contoso.com` 目前易受攻击，可通过将其分配到其他 Azure 订阅的资源来接管它。

1. **接管：**

    1. 威胁操纵者使用常用方法和工具来发现无关联的子域。  

    1. 威胁操纵者使用之前由你控制的资源的 FQDN 来预配 Azure 资源。 在本示例中为 `app-contogreat-dev-001.azurewebsites.net`。

    1. 发送到子域 `greatapp.contoso.com` 的流量现在路由到由恶意操作者控制其中内容的资源。



![取消预配的网站中的子域接管](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管造成的风险

当 DNS 记录指向不可用的资源时，记录本身应已从 DNS 区域中删除。 如果尚未删除，它会成为“无关联的 DNS”记录，并且有可能发生子域接管。

无关联的 DNS 项使威胁操纵者能够控制关联的 DNS 名称，进而托管恶意网站或服务。 组织子域中的恶意页面和服务可能导致以下结果：

- **失去对子域内容的控制** - 造成负面影响，显示出你的组织没法保护自身的内容，还会造成品牌受损和信任丧失。

- **从无防备的访客那里获取 Cookie** - Web 应用通常会将会话 Cookie 公开到子域 (*.contoso.com)，这导致任何子域均可对其进行访问。 威胁操纵者可能会使用子域接管来生成看似可信的页面，诱使毫无戒心的用户访问该页面，然后获取用户的 Cookie（甚至是安全 Cookie）。 常见的误解是，使用 SSL 证书可保护网站和用户的 Cookie 免遭接管。 然后，威胁操纵者可能会使用劫持的子域来申请和接收有效的 SSL 证书。 有效的 SSL 证书使他们能够访问安全 Cookie，而且可进一步使恶意网站看起来更合法。

- **网络钓鱼活动** - 看似可信的子域可能会被用于网络钓鱼活动。 对于恶意网站和 MX 记录来说确实如此，威胁操纵者会利用这些内容来接收发往已知安全品牌的合法子域的电子邮件。

- **其他风险** - 恶意网站可能会被用于升级为其他经典攻击，例如 XSS、CSRF 和 CORS 绕过等。



## <a name="identify-dangling-dns-entries"></a>标识无关联的 DNS 项

若要标识组织内部可能无关联的 DNS 项，请使用 Microsoft 的 GitHub 托管的 PowerShell 工具[“Get-DanglingDnsRecords”](https://aka.ms/DanglingDNSDomains)。

此工具可帮助 Azure 客户列出 CNAME 与客户订阅或租户上创建的现有 Azure 资源相关联的所有域。

如果 CNAME 属于其他 DNS 服务并指向 Azure 资源，请在该工具的输入文件中提供 CNAME。

该工具支持下表中列出的 Azure 资源。 该工具会提取所有租户的 CNAME 或将其作为输入。


| 服务                   | 类型                                        | FQDNproperty                               | 示例                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | properties.cName                           | `abc.azurefd.net`               |
| Azure Blob 存储        | microsoft.storage/storageaccounts           | properties.primaryEndpoints.blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties.hostName                        | `abc.azureedge.net`             |
| 公共 IP 地址       | microsoft.network/publicipaddresses         | properties.dnsSettings.fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure 流量管理器     | microsoft.network/trafficmanagerprofiles    | properties.dnsConfig.fqdn                  | `abc.trafficmanager.net`        |
| Azure 容器实例  | microsoft.containerinstance/containergroups | properties.ipAddress.fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API 管理      | microsoft.apimanagement/service             | properties.hostnameConfigurations.hostName | `abc.azure-api.net`             |
| Azure 应用服务         | microsoft.web/sites                         | properties.defaultHostName                 | `abc.azurewebsites.net`         |
| Azure 应用服务 - 槽 | microsoft.web/sites/slots                   | properties.defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>先决条件

以具有下列权限的用户身份运行查询：

- 至少可对 Azure 订阅进行读取者级别访问
- 可对 Azure Resource Graph 进行读取访问

如果你是贵组织租户的全局管理员，请按照[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)中的指导，将帐户提升为可访问贵组织的所有订阅。


> [!TIP]
> 如果使用大型 Azure 环境，应考虑 Azure Resource Graph 的带宽限制和分页限制。 
> 
> [详细了解如何使用大型 Azure 资源数据集](../../governance/resource-graph/concepts/work-with-data.md)。
> 
> 该工具使用订阅批处理来避免这些限制。

### <a name="run-the-script"></a>运行脚本

详细了解 PowerShell 脚本 Get-DanglingDnsRecords.ps1，然后从 GitHub 下载： https://aka.ms/DanglingDNSDomains。

## <a name="remediate-dangling-dns-entries"></a>修正无关联的 DNS 项 

查看 DNS 区域，并标识无关联的或已遭接管的 CNAME 记录。 如果发现子域无关联或已遭接管，请删除易受攻击的子域，并按以下步骤操作来缓解风险：

1. 在 DNS 区域中，删除所有指向不再预配的资源的 FQDN 的 CNAME 记录。

1. 为确保流量路由到你控制的资源，请使用无关联子域的 CNAME 记录中指定的 FQDN 来预配其他资源。

1. 查看应用程序代码中对特定子域的引用，并更新所有错误或过期的子域引用。

1. 调查是否发生了任何泄露，并按照组织的事件响应程序采取措施。 以下是调查此问题的提示和最佳做法。

    如果应用程序逻辑是将 OAuth 凭据等机密发送到无关联的子域，或将隐私敏感信息发送到无关联的子域，则该数据可能已公开到第三方。

1. 了解取消预配资源时未从 DNS 区域中删除 CNAME 记录的原因，并采取措施以确保后续取消预配 Azure 资源时正确更新 DNS 记录。


## <a name="prevent-dangling-dns-entries"></a>防止无关联的 DNS 项

确保贵组织已落实过程来防止出现无关联的 DNS 项，安全计划的关键部分是防止由这些项产生的子域接管。

一些 Azure 服务提供了相关功能来帮助创建预防措施，下面是详细介绍。 必须根据贵组织的最佳做法或标准操作程序来建立防止此问题的其他方法。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 别名记录

Azure DNS 的[别名记录](../../dns/dns-alias.md#scenarios)通过将 DNS 记录的生命周期与 Azure 资源相结合来防止出现无关联引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源，DNS 别名记录会变成空的记录集。 它不再引用已删除的资源。 务必注意，别名记录可保护的内容有限。 目前，列表限制如下：

- Azure Front Door
- 流量管理器配置文件
- Azure 内容分发网络 (CDN) 终结点
- 公共 IP

尽管目前服务产品有限，但建议尽可能使用别名记录来防止子域接管。

[详细了解 Azure DNS 的别名记录的功能](../../dns/dns-alias.md#capabilities)。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure 应用服务的自定义域验证

为 Azure 应用服务创建 DNS 项时，请使用域验证 ID 创建 asuid.{subdomain} TXT 记录。 存在此类 TXT 记录时，其他 Azure 订阅均无法验证自定义域，即无法接管自定义域。 

这些记录不会阻止使用 CNAME 项中的名称创建 Azure 应用服务。 如果无法证明域名的所有权，则威胁操纵者无法接收流量或控制内容。

[详细了解如何将现有的自定义 DNS 名称映射到 Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>建立并自动执行缓解威胁的流程

通常是由开发人员和操作团队来运行清理进程，以避免无关联的 DNS 威胁。 以下做法有助于确保贵组织免遭这种威胁。 

- **创建预防过程：**

    - 训练应用程序开发人员在每次删除资源时都重新路由地址。

    - 将“删除 DNS 项”放入停用服务时需进行的检查的列表中。

    - 在具有自定义 DNS 项的所有资源中放入[删除锁定](../../azure-resource-manager/management/lock-resources.md)。 删除锁定可指示取消预配资源前必须删除映射。 只有在与内部训练计划结合时，此类措施才能发挥作用。

- **创建发现过程：**

    - 定期查看 DNS 记录，确保所有子域都映射到以下 Azure 资源：

        - 存在-在 DNS 区域中查询指向 Azure 子域的资源，例如 *. azurewebsites.net 或 *. cloudapp.azure.com (参阅 [azure 域) 的参考列表](azure-domains.md) 。
        - You own - 确认拥有 DNS 子域面向的所有资源。

    - 维护包含 Azure 完全限定的域名 (FQDN) 终结点和应用程序所有者的服务目录。 若要生成服务目录，请运行以下 Azure Resource Graph 查询脚本。 此脚本阐述你有权访问的资源的 FQDN 终结点信息，并将其以 CSV 文件形式输出。 如果你可访问租户的所有订阅，则脚本将考虑所有这些订阅，如以下示例脚本所示。 若要将结果限制为一组特定的订阅，请如下所示编辑脚本。


- **创建修正过程：**
    - 找到无关联的 DNS 项后，团队需要调查是否发生了任何泄露。
    - 调查停用资源时未重新路由地址的原因。
    - 如果不再使用 DNS 记录，请将其删除，或将其指向贵组织所拥有的正确 Azure 资源 (FQDN)。
 

## <a name="next-steps"></a>后续步骤

若要详细了解可用于防止子域接管的相关服务和 Azure 功能，请参阅以下页面。

- [防止无关联的 DNS 记录与 Azure DNS](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [在 Azure App Service 中添加自定义域时使用域验证 ID](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [快速入门：使用 Azure PowerShell 运行首个 Resource Graph 查询](../../governance/resource-graph/first-query-powershell.md)
