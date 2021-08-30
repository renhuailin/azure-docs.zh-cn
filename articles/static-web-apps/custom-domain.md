---
title: 在 Azure Static Web Apps 中设置自定义域
description: 了解如何将具有免费 SSL/TLS 证书的自定义域映射到 Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: buhollan
ms.openlocfilehash: 07da31c48d70f25a2364b4af242bc0d1331d7036
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750668"
---
# <a name="set-up-a-custom-domain-with-free-certificate-in-azure-static-web-apps"></a>在 Azure Static Web Apps 中设置具有免费证书的自定义域

Azure 静态 Web 应用默认提供自动生成的域名。 本文介绍如何将自定义域名映射到 Azure 静态 Web 应用应用程序。

## <a name="free-ssltls-certificate"></a>免费的 SSL/TLS 证书

Azure Static Web Apps 自动为自动生成的域名以及你可以添加的任何自定义域提供免费的 SSL/TLS 证书。

## <a name="walkthrough-video"></a>演练视频

> [!VIDEO https://channel9.msdn.com/Shows/5-Things/Configuring-a-custom-domain-with-Azure-Static-Web-Apps/player?format=ny]

## <a name="prerequisites"></a>先决条件

- 一个购买的域名
- 访问域的 DNS 配置属性

## <a name="dns-configuration-options"></a>DNS 配置选项

有几种不同类型的 DNS 配置可用于应用程序。

| 方案                                                                                 | 示例                                | 域验证方法 | DNS 记录类型 |
| ---------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------ | --------------- |
| [添加根/顶点域](#add-domain-using-txt-record-validation)                        | `mydomain.com`, `example.co.uk`        | TXT                      | ALIAS           |
| [添加子域](#add-domain-using-cname-record-validation)                             | `www.mydomain.com`, `foo.mydomain.com` | CNAME                    | CNAME           |
| [转移当前正在使用的子域](#add-domain-using-txt-record-validation) | `www.mydomain.com`, `foo.mydomain.com` | TXT                      | CNAME           |

## <a name="add-domain-using-cname-record-validation"></a>使用 CNAME 记录验证添加域

CNAME 记录验证是建议用于添加自定义域的方式，但是，它仅适用于子域。 如果你要添加根域 (`mydomain.com`)，请跳转到[使用 TXT 记录验证添加域](#add-domain-using-txt-record-validation)，然后[创建 ALIAS 记录](#create-an-alias-record)。

> [!IMPORTANT]
> 如果子域当前已关联到实时站点，并且你尚未准备好将其转移到静态 Web 应用，请使用 [TXT 记录验证](#add-domain-using-txt-record-validation)。

### <a name="enter-your-subdomain"></a>输入子域

1. 在 [Azure 门户](https://portal.azure.com)中打开你的静态 Web 应用。

1. 在菜单中选择“自定义域”。

1. 选择“添加”按钮。

1. 在“域名”字段中输入你的子域。 输入子域时，请确保不要输入任何协议。 例如 `www.mydomain.com`。

   :::image type="content" source="media/custom-domain/add-subdomain.png" alt-text="“添加域”屏幕，其中显示了“输入”框内的自定义子域":::

1. 选择“下一步”按钮转到“验证 + 配置”步骤。

### <a name="configure-cname-with-your-domain-provider"></a>在域提供商的配合下配置 CNAME

需要在域提供商的配合下配置 CNAME。 建议配置 Azure DNS，但这些步骤需要在任一域提供商的配合下完成。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. 确保从“主机名记录类型”下拉列表中选择“CNAME”。

1. 选择“复制”图标将“值”字段中的值复制到剪贴板。

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="“验证 + 配置”屏幕，其中显示了用红框标示的选定“CNAME”和复制图标":::

1. 通过另一个浏览器标签页或窗口在 Azure 门户中打开你的 Azure DNS 区域。

1. 选择“+ 记录集”按钮。

1. 使用以下值创建新的 CNAME 记录集。

   | 设置          | 值                                     |
   | ---------------- | ----------------------------------------- |
   | 名称             | 你的子域，例如 `www`             |
   | 类型             | CNAME                                     |
   | 别名记录集 | 否                                        |
   | TTL              | 保留为默认值                    |
   | TTL 单位         | 保留为默认值                    |
   | Alias            | 粘贴剪贴板中的域名 |

1. 选择“确定”。

   :::image type="content" source="media/custom-domain/azure-dns-cname.png" alt-text="“Azure DNS 记录集”屏幕，其中突出显示了名称、类型和别名字段":::

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

# <a name="other-dns"></a>[其他 DNS](#tab/other-dns)

1. 确保从“主机名记录类型”下拉列表中选择“CNAME”。

1. 选择“复制”图标将“值”字段中的值复制到剪贴板。

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="“验证 + 添加”屏幕，其中显示了用红框标示的选定“CNAME”和复制图标":::

1. 在另一个浏览器标签页或窗口中登录到域提供商的网站。

1. 查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。  

1. 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。

   以下屏幕截图是 DNS 记录页的一个示例：

   :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="DNS 提供程序配置示例":::

1. 使用以下值创建新的 CNAME 记录。

   | 设置             | 值                                     |
   | ------------------- | ----------------------------------------- |
   | 类型                | CNAME                                     |
   | 主机                | 你的子域，例如 `www`             |
   | 值               | 粘贴剪贴板中的域名 |
   | TTL（如果适用） | 保留为默认值                    |

1. 保存 DNS 提供程序所做的更改。

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

---

## <a name="add-domain-using-txt-record-validation"></a>使用 TXT 记录验证添加域

Azure 使用 TXT 记录来验证你是否拥有某个域。 当你想要执行以下操作之一时，此功能非常有用...

1. 你想要配置根域（即 `mydomain.com`）。 在你创建用于配置根域的 ALIAS 记录之前，需要先验证你是否拥有该域。

1. 你想要在不停机的情况下转移子域。 使用 TXT 记录验证方法可以验证你自己是否拥有该域，并使静态 Web 应用能够完成颁发该域的证书的过程。 然后，可以随时使用 CNAME 记录将域切换为指向你的静态 Web 应用。

#### <a name="enter-your-domain"></a>输入域

1. 在 [Azure 门户](https://portal.azure.com)中打开你的静态 Web 应用。

1. 在菜单中选择“自定义域”。

1. 选择“添加”按钮。

1. 在“域名”字段中，输入根域（即 `mydomain.com`）或子域（即 `www.mydomain.com`）。

   :::image type="content" source="media/custom-domain/add-domain.png" alt-text="“添加域”屏幕，其中显示了“输入”框内的自定义域":::

1. 单击“下一步”按钮转到“验证 + 配置”步骤。

#### <a name="configure-txt-record-with-your-domain-provider"></a>在域提供商的配合下配置 TXT 记录

需要在域提供商的配合下配置 TXT 记录。 建议配置 Azure DNS，但这些步骤需要在任一域提供商的配合下完成。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. 确保“主机名记录类型”下拉列表设置为“TXT”。

1. 选择“生成代码”按钮。

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="“添加自定义域”屏幕，其中突出显示了“生成代码”按钮":::

   此操作将生成唯一的代码，该过程可能最多需要一分钟的时间。

1. 选择代码旁边的剪贴板图标以将值复制到剪贴板。

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="“添加自定义域”屏幕，其中突出显示了“复制代码”按钮":::

1. 通过另一个浏览器标签页或窗口在 Azure 门户中打开你的 Azure DNS 区域。

1. 选择“+ 记录集”按钮。

1. 使用以下值创建新的 TXT 记录集。

   | 设置  | 值                                                                           |
   | -------- | ------------------------------------------------------------------------------- |
   | 名称     | `@` 表示根域，或输入 `_dnsauth.<YOUR_SUBDOMAIN>` 以表示子域         |
   | 类型     | TXT                                                                             |
   | TTL      | 保留为默认值                                                          |
   | TTL 单位 | 保留为默认值                                                          |
   | 值    | 粘贴剪贴板中的代码                                              |

1. 选择“确定”。

   :::image type="content" source="media/custom-domain/azure-dns-txt.png" alt-text="“Azure DNS 记录集”屏幕，其中突出显示了名称、类型和值字段":::

[!INCLUDE [validate TXT record](../../includes/static-web-apps-validate-txt.md)]

# <a name="other-dns"></a>[其他 DNS](#tab/other-dns)

1. 确保“主机名记录类型”下拉列表设置为“TXT”。

1. 选择“生成代码”按钮。

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="“添加自定义域”屏幕，其中突出显示了“生成代码”按钮":::

   此操作将生成唯一的代码，该过程可能最多需要一分钟的时间。

1. 选择代码旁边的剪贴板图标以将值复制到剪贴板。

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="“添加自定义域”屏幕，其中突出显示了“复制代码”按钮":::

1. 在另一个浏览器标签页或窗口中登录到域提供商的网站。

1. 查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。  

   > [!NOTE]
   > 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。

1. 使用以下值创建新的 TXT 记录...

   | 设置             | 值                                                                        |
   | ------------------- | ---------------------------------------------------------------------------- |
   | 类型                | TXT                                                                          |
   | 主机                | `@` 表示根域，或输入 `_dnsauth.<YOUR_SUBDOMAIN>` 以表示子域      |
   | 值               | 粘贴剪贴板中的代码                                           |
   | TTL（如果适用） | 保留为默认值                                                       |

> [!NOTE]
> 某些 DNS 提供程序使用与“@”不同的约定来指示根域，或者它们会自动将“@”更改为你的根域（即 mydomain.com）。 这在意料之内，验证过程仍会正常进行。

[!INCLUDE [create repository from template](../../includes/static-web-apps-validate-txt.md)]

---

## <a name="create-an-alias-record"></a>创建 ALIAS 记录

ALIAS 记录将一个域映射到另一个域。 此记录专用于根域（即 `mydomain.com`）。 在本部分，你将创建一条 ALIAS 记录，用于将根域映射到自动生成的静态 Web 应用 URL。

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

> [!IMPORTANT]
> Azure DNS 区域应与静态 Web 应用位于同一订阅中。

1. 在 Azure 门户中打开域的 Azure DNS 区域。

1. 选择“+ 记录集”按钮。

1. 使用以下值创建新的 A 记录集。

   | 设置          | 值                              |
   | ---------------- | ---------------------------------- |
   | 名称             | @                                  |
   | 类型             | A - 指向 IPv4 地址的别名记录   |
   | 别名记录集 | 是                                |
   | 别名类型       | Azure 资源                     |
   | 订阅     | \<Your Subscription>               |
   | Azure 资源   | \<Your Static Web App>             |
   | TTL              | 保留为默认值             |
   | TTL 单位         | 保留为默认值             |

1. 选择“确定”。

   :::image type="content" source="media/custom-domain/azure-dns-alias.png" alt-text="“Azure DNS 记录集”屏幕，其中突出显示了名称、类型、别名和资源字段":::

现已配置根域，DNS 提供程序可能需要几个小时才能在全球范围内传播更改。

# <a name="other-dns"></a>[其他 DNS](#tab/other-dns)

> [!IMPORTANT]
> 域提供程序必须支持 [ALIAS](../dns/dns-alias.md) 或 ANAME 记录，或支持 CNAME 平展。

1. 在 [Azure 门户](https://portal.azure.com)中打开你的静态 Web 应用。

1. 在菜单中选择“自定义域”。

1. 从“自定义域”屏幕中复制自动生成的静态 Web 应用 URL。

   :::image type="content" source="media/custom-domain/auto-generated.png" alt-text="静态 Web 应用的“概述”页，其中突出显示了“复制 URL”图标":::

1. 请登录到域提供商的网站。

1. 查找管理 DNS 记录的页面。 每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。  

   > [!NOTE]
   > 通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。

1. 使用以下值创建新的 ALIAS 记录...

   | 设置             | 值                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | 类型                | ALIAS 或 ANAME（如果 ALIAS 不可用，请使用 CNAME）                    |
   | 主机                | @                                                              |
   | 值               | 粘贴剪贴板中的域名                      |
   | TTL（如果适用） | 保留为默认值                                         |

> [!IMPORTANT]
> 如果域提供商不提供 ALIAS 或 ANAME 记录类型，请改用 CNAME 类型。 许多提供商通过 CNAME 记录类型以及一个称作“CNAME 平展”的功能来提供与 ALIAS 记录类型相同的功能。

现已配置根域，DNS 提供程序可能需要几个小时才能在全球范围内传播更改。

---

## <a name="redirect-requests-to-a-default-domain"></a>将请求重定向到默认域

可以使用静态 Web 应用自动生成的域以及你配置的任何自定义域访问该静态 Web 应用。 或者，你可以将应用配置为将所有流量重定向到默认域。

### <a name="set-a-default-domain"></a>设置默认域

将自定义域指定为应用的默认域时，对其他域的请求会自动重定向到该默认域。 只能将一个自定义域设置为默认域。

请按照以下步骤将自定义域设置为默认域。

1. 在 Azure 门户中打开静态 Web 应用后，选择菜单中的“自定义域”。

1. 选择要配置为默认域的自定义域。

1. 选择“设为默认域”。

   :::image type="content" source="media/custom-domain/set-default.png" alt-text="将自定义域设置为默认域":::

1. 操作完成后，刷新表以确认该域被标记为“默认域”。

### <a name="unset-a-default-domain"></a>取消设置默认域

要让域停止重定向到默认域，请执行以下步骤。

1. 在 Azure 门户中打开静态 Web 应用后，选择菜单中的“自定义域”。

1. 选择配置为默认域的自定义域。

1. 选择“取消设为默认域”。

1. 操作完成后，刷新表以确认没有域被标记为“默认域”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](application-settings.md)
