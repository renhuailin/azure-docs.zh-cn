---
title: 教程：映射现有的自定义 DNS 名称
description: 了解如何在 Azure 应用服务中向 Web 应用、移动应用后端或 API 应用添加现有的自定义 DNS 域名（虚域）。
keywords: 应用服务, azure 应用服务, 域映射, 域名, 现有域, 主机名, 虚域
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32ad6fa122083b40a948345e360bf5b9b0f09e96
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954847"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>教程：将现有的自定义 DNS 名称映射到 Azure 应用服务

本教程介绍如何将现有 <abbr title="从域注册机构（如 GoDaddy）或已购买的域的子域中购买的域名。">自定义 DNS 域名</abbr> to <abbr title="一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端应用程序。">Azure 应用服务</abbr>.

本教程介绍以下操作：

> [!div class="checklist"]
> * 映射子域，方法是使用 <abbr title="DNS 规范名称记录会将一个域名映射到另一个域名。">CNAME 记录</abbr>.
> * 映射根域，方法是使用 <abbr title="DNS 中的地址记录会将主机名映射到 IP 地址。">A 记录</abbr>.
> * 使用 CNAME 记录映射通配符域。
> * 将默认 URL 重定向到自定义目录。

<hr/> 

## <a name="1-prepare-your-environment"></a>1.准备环境

* [创建应用服务应用](./index.yml)，或使用为另一教程创建的应用。
* 请确保可编辑自定义域的 DNS 记录。 如果还没有自定义域，可[购买应用服务域](manage-custom-dns-buy-domain.md)。

    <details>
        <summary>要满足哪些条件才能编辑 DNS 记录？</summary>
        需要有权访问域提供商（例如 GoDaddy）的 DNS 注册表。 例如，若要添加 <code>contoso.com</code> 和 <code>www.contoso.com</code> 的 DNS 条目，必须能够配置 <code>contoso.com</code> 根域的 DNS 设置。
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2.准备应用

若要将自定义 DNS 名称映射到应用，应用的 <abbr title="指定托管应用的 Web 服务器场的位置、大小和功能。">应用服务计划</abbr> 必须是付费层（不 <abbr title="Azure 应用服务层，你的应用在该层与其他应用（包括其他客户的应用）在相同的 VM 上运行。 此层级用于开发和测试。">免费 (F1)</abbr>). 有关详细信息，请参阅 [Azure 应用服务计划概述](overview-hosting-plans.md)。

#### <a name="sign-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

#### <a name="select-the-app-in-the-azure-portal"></a>在 Azure 门户中选择应用

1. 搜索并选择“应用服务”。

   ![演示如何选择应用服务的屏幕截图。](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 在“应用服务”页上，选择 Azure 应用的名称。

   ![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/select-app.png)

    将看到应用服务应用的管理页。

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>检查定价层

1. 在应用页的左侧窗格中，向下滚动到“设置”部分，然后选择“纵向扩展(应用服务计划)” 。

   ![显示“纵向扩展(应用服务计划)”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 蓝色边框突出显示了应用的当前层。 检查以确保应用不在 F1 层中。 F1 层不支持自定义 DNS。

   ![显示“建议的定价层”的屏幕截图。](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. 如果应用服务计划不在 F1 层中，请关闭“纵向扩展”页面并跳转到 [3.获取域验证 ID](#3-get-a-domain-verification-id)。

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

1. 选择任何非免费层（**D1**、**B1**、**B2**、**B3**，或“生产”类别中的任何层）。 有关其他选项，请选择“查看其他选项”。

1. 选择“应用”。

   ![显示检查定价层的屏幕截图。](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   看到以下通知时，说明缩放操作已完成。

   ![显示缩放操作确认的屏幕截图。](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3.获取域验证 ID

若要将自定义域添加到应用，需要使用域提供程序添加验证 ID 作为 TXT 记录来验证你对域的所有权。 

1. 在应用页的左侧窗格中，选择“自定义域”。 
1. 复制“自定义域”页中的“自定义域验证 ID”框中的 ID，以便进行下一步操作 。

    ![显示“自定义域验证 ID”框中的 ID 的屏幕截图。](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>为何需要执行此操作？</summary>
        向自定义域添加域验证 ID 可防止出现无关联的 DNS 条目，并避免子域接管。 对于先前配置的没有此验证 ID 的自定义域，应将验证 ID 添加到 DNS 记录中，以防止这些域面临相同的风险。 有关此常见高严重性威胁的详细信息，请参阅<a href="/azure/security/fundamentals/subdomain-takeover">子域接管</a>。
    </details>
    
<a name="info"></a>

3. （仅限 A 记录）若要映射 <abbr title="DNS 中的地址记录会将主机名映射到 IP 地址。">A 记录</abbr>，需要应用的外部 IP 地址。 在“自定义域”页面中，复制 IP 地址的值 。

   ![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4.创建 DNS 记录

1. 请登录到域提供商的网站。

    <details>
        <summary>能否使用 Azure 从我的域提供程序管理 DNS？</summary>
        如果需要，可使用 Azure DNS 来管理域的 DNS 记录，并为 Azure 应用服务配置自定义 DNS 名称。 有关详细信息，请参阅<a href="/azure/dns/dns-delegate-domain-azure-dns">教程：在 Azure DNS 中托管域</a>。
    </details>

1. 查找管理 DNS 记录的页面。 

    <details>
        <summary>如何找到该页面？</summary>
        <p>每个域提供商都有自己的 DNS 记录界面，因此请查阅提供商的文档。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。   </p>
        <p>通常通过查看帐户信息，然后查找如“我的域”之类的链接，便可以找到 DNS 记录页面。 转到该页面，然后查找名称类似于“区域文件”、“DNS 记录”或“高级配置”的链接  。</p>
    </details>

   以下屏幕截图是 DNS 记录页的一个示例：

   ![显示示例 DNS 记录页的屏幕截图。](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. 选择“添加”或相应的小组件来创建记录。 

1. 选择要创建的记录的类型，然后按照说明进行操作。 可使用 <abbr title="DNS 中的规范名称记录会将一个域名（别名）映射到另一个域名（规范名称）。">CNAME 记录</abbr> 或 <abbr title="DNS 中的地址记录会将主机名映射到 IP 地址。">A 记录</abbr> 将自定义 DNS 名称映射到应用服务。 

    <details>
        <summary>我该选择哪一个记录？</summary>
        <div>
            <ul>
            <li>若要映射根域（例如 <code>contoso.com</code>），请使用 A 记录。 请勿使用根记录的 CNAME 记录（有关信息，请参阅<a href="https://en.wikipedia.org/wiki/CNAME_record">维基百科条目</a>）。</li>
            <li>若要映射子域（例如 <code>www.contoso.com</code>），请使用 CNAME 记录。</li>
            <li>可使用 A 记录直接将子域映射到应用的 IP 地址，但 <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP 地址</a>可能会更改。 CNAME 改为映射到应用的主机名，该主机名不易更改。</li>
            <li>若要映射<a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">通配符域</a>（例如 <code>*.contoso.com</code>），请使用 CNAME 记录。</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

对于子域（如 `www.contoso.com` 中的 `www`），请根据下表创建两条记录：

| 记录类型 | 主机 | 值 | 注释 |
| - | - | - |
| CNAME | `<subdomain>`（例如 `www`） | `<app-name>.azurewebsites.net` | 域映射本身。 |
| TXT | `asuid.<subdomain>`（例如 `asuid.www`） | [之前获得的验证 ID](#3-get-a-domain-verification-id) | 应用服务访问 `asuid.<subdomain>` TXT 记录以验证你对自定义域的所有权。 |

![显示到 Azure 应用的门户导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

对于根域（如 `contoso.com`），请根据下表创建两条记录：

| 记录类型 | 主机 | 值 | 注释 |
| - | - | - |
| A | `@` | 通过[复制应用的 IP 地址](#3-get-a-domain-verification-id)获得的 IP 地址 | 域映射本身（`@` 通常表示根域）。 |
| TXT | `asuid` | [之前获得的验证 ID](#3-get-a-domain-verification-id) | 应用服务访问 `asuid.<subdomain>` TXT 记录以验证你对自定义域的所有权。 对于根域，请使用 `asuid`。 |

![显示 DNS 记录页的屏幕截图。](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>如果我想用 A 记录映射子域，该怎么办？</summary>
若要使用 A 记录（而不是建议的 CNAME 记录）映射子域（如 `www.contoso.com`），A 记录和 TXT 记录应改为类似于下表：

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">表 3</caption>
<thead>
<tr>
<th>记录类型</th>
<th>主机</th>
<th>值</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code>（例如 <code>www</code>）</td>
<td>通过<a href="#info" data-linktype="self-bookmark">复制应用的 IP 地址</a>获得的 IP 地址</td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code>（例如 <code>asuid.www</code>）</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">之前获得的验证 ID</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[通配符 (CNAME)](#tab/wildcard)

对于通配符名称（如 `*.contoso.com` 中的 `*`），请按照下表创建两条记录：

| 记录类型 | 主机 | 值 | 注释 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | 域映射本身。 |
| TXT | `asuid` | [之前获得的验证 ID](#3-get-a-domain-verification-id) | 应用服务访问 `asuid` TXT 记录以验证你对自定义域的所有权。 |

![显示到 Azure 应用的导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>离开页面后，所做的更改将被清除。</summary>
<p>对于某些提供商（例如 GoDaddy），在你选择单独的“保存更改”链接之前，这些 DNS 记录不会生效  。</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5.在应用中启用映射

1. 在 Azure 门户中的应用页左侧窗格中，选择“自定义域”。

    ![显示“自定义域”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 选择“添加自定义域”。

    ![显示“添加主机名”项的屏幕截图。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. 键入已添加 CNAME 记录的完全限定的域名，如 `www.contoso.com`。

1. 选择“验证”。 此时将显示“添加自定义域”页。

1. 确保“主机名记录类型”设置为“CNAME (www\.example.com 或任何子域)”。 选择“添加自定义域”。

    ![显示“添加自定义域”按钮的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    ![演示如何添加 CNAME 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>“不安全”警告标签有何含义？</summary>
        自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅<a href="/azure/app-service/configure-ssl-bindings">在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称</a>。
    </details>

    如果之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部显示验证错误。

    ![显示验证错误的屏幕截图。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. 键入已配置 A 记录的完全限定的域名，如 `contoso.com`。 

1. 选择“验证”。  此时会显示“添加自定义域”页。

1. 确保“主机名记录类型”设置为“A 记录 (example.com)”。  选择“添加自定义域”。

    ![演示如何将主机名添加到应用的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    ![演示如何添加 A 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>“不安全”警告标签有何含义？</summary>
        自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅<a href="/azure/app-service/configure-ssl-bindings">在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称</a>。
    </details>
    
    如果之前错过了某个步骤或者在某个位置的输入不正确，则会在页面的底部显示验证错误。
    
    ![显示验证错误的屏幕截图。](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[通配符 (CNAME)](#tab/wildcard)

3. 键入与通配符域匹配的完全限定的域名。 以 `*.contoso.com` 为例，可使用 `sub1.contoso.com`、`sub2.contoso.com`、`*.contoso.com` 或与通配符模式匹配的任何其他字符串。 然后，选择“验证”。

    “添加自定义域”按钮随即激活。

1. 确保“主机名记录类型”设置为“CNAME 记录(www\.example.com 或任何子域)”。 选择“添加自定义域”。

    ![显示将 DNS 名称添加到应用的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    <details>
        <summary>“不安全”警告标签有何含义？</summary>
        自定义域带有警告标签意味着该域尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅<a href="/azure/app-service/configure-ssl-bindings">在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称</a>。
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6.在浏览器中测试

浏览至你之前配置的 DNS 名称。

![显示到 Azure 应用的导航的屏幕截图。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>我收到 HTTP 404（找不到）错误。</summary>
<ul>
<li>配置的自定义域缺少 A 记录或 CNAME 记录。</li>
<li>浏览器客户端已缓存域的旧 IP 地址。 清除缓存并再次测试 DNS 解析。 在 Windows 计算机上，使用 <code>ipconfig /flushdns</code> 清除缓存。</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>迁移活动域

若要将实时站点及其 DNS 域名迁移到应用服务而不停机，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](manage-custom-dns-migrate-domain.md)。

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>重定向到自定义目录

<details>
<summary>是否需要执行此操作？</summary>
<p>这取决于你的应用。 默认情况下，应用服务将 Web 请求定向到应用代码的根目录下。 但某些 Web 框架不在根目录下启动。 例如，<a href="https://laravel.com/">Laravel</a> 在 <code>public</code> 子目录中启动。 若要继续 <code>contoso.com</code> DNS 示例，此类应用可在 <code>http://contoso.com/public</code> 中访问，但你想要将 <code>http://contoso.com</code> 直接定向到 <code>public</code> 目录。 </p>
</details>

虽然此方案很常见，但实际上，它不涉及自定义域映射，而且关乎到在应用中自定义虚拟目录。

1. 在 Web 应用页的左侧窗格中选择“应用程序设置”。

1. 在页面底部，根虚拟目录 `/` 默认指向 `site\wwwroot`，这是应用代码的根目录。 将其改为指向例如 `site\wwwroot\public`，并保存所做的更改。

    ![演示如何自定义虚拟目录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. 操作完成后，请导航到浏览器（例如 `http://contoso.com` 或 `http://<app-name>.azurewebsites.net`）中应用的根路径进行验证。

<hr/> 

## <a name="automate-with-scripts"></a>使用脚本自动化

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/) 通过脚本自动管理自定义域。

#### <a name="azure-cli"></a>Azure CLI

以下命令将配置的自定义 DNS 名称添加到应用服务应用。

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

有关详细信息，请参阅[将自定义域映射到 Web 应用](scripts/cli-configure-custom-domain.md)。

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下命令将配置的自定义 DNS 名称添加到应用服务应用。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

有关详细信息，请参阅[将自定义域分配到 Web 应用](scripts/powershell-configure-custom-domain.md)。

<hr/> 

## <a name="next-steps"></a>后续步骤

继续学习下一教程，了解如何将自定义 TLS/SSL 证书绑定到 Web 应用。

> [!div class="nextstepaction"]
> [在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)