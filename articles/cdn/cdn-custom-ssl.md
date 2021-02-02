---
title: 教程：在 Azure CDN 自定义域中配置 HTTPS
description: 在本教程中，你将了解如何在 Azure CDN 终结点自定义域上启用和禁用 HTTPS。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c4ad270b989e0e212c1d362ae4bfafc91fe07f3e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943548"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>教程：在 Azure CDN 自定义域中配置 HTTPS

本教程演示如何为与 Azure CDN 终结点关联的自定义域启用 HTTPS 协议。 

自定义域上的 HTTPS 协议（例如 https:\//www.contoso.com）可确保敏感数据通过 TLS/SSL 安全地进行分发。 当 Web 浏览器通过 HTTPS 连接时，浏览器将验证网站的证书。 浏览器会验证它是否由合法的证书颁发机构颁发。 此过程提供安全性并保护 Web 应用程序免受攻击。

默认情况下，Azure CDN 支持对 CDN 终结点主机名使用 HTTPS。 例如，如果创建 CDN 终结点（例如 https:\//contoso.azureedge.net），则会自动启用 HTTPS。  

自定义 HTTPS 功能的一些关键属性包括：

- 无额外费用：可免费获取或续订证书，对 HTTPS 流量也不额外收费。 只需为从 CDN 出口的 GB 数付费。

- 简单启用：可从 [Azure 门户](https://portal.azure.com)进行一键式预配。 还可以使用 REST API 或其他开发人员工具启用该功能。

- 提供完整的证书管理： 
    * 为你处理所有证书获取和管理。 
    * 在证书过期之前自动预配并续订证书。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 在自定义域上启用 HTTPS 协议。
> - 使用 CDN 托管的证书 
> - 使用自己的证书
> - 验证域
> - 在自定义域上禁用 HTTPS 协议。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

在完成本教程中的步骤之前，先创建 CDN 配置文件和至少一个 CDN 终结点。 有关详细信息，请参阅[快速入门：创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

在 CDN 终结点上关联 Azure CDN 自定义域。 有关详细信息，请参阅[教程：将自定义域添加到 Azure CDN 终结点](cdn-map-content-to-custom-domain.md)。

> [!IMPORTANT]
> CDN 托管的证书不可用于根域或顶点域。 如果 Azure CDN 自定义域是根域或顶点域，则必须使用“自带证书”功能。 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL 证书

若要在 Azure CDN 自定义域上启用 HTTPS，请使用 TLS/SSL 证书。 可选择是使用 Azure CDN 托管的证书还是自己的证书。

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[选项 1（默认）：使用 CDN 托管的证书启用 HTTPS](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN 可处理证书管理任务，例如获取和续订操作。 启用此功能后，进程将立即启动。 

如果自定义域已映射到 CDN 终结点，则无需进一步操作。 Azure CDN 将自动执行步骤并完成请求。

如果自定义域映射到其他位置，请使用电子邮件来验证域所有权。

若要在自定义域上启用 HTTPS，请执行以下步骤：

1. 请参阅 [Azure 门户](https://portal.azure.com)，查找 Azure CDN 托管的证书。 搜索并选择“CDN 配置文件”。  

2. 选择配置文件：
    * **来自 Microsoft 的 Azure CDN 标准版**
    * **来自 Akamai 的 Azure CDN 标准版**
    * **来自 Verizon 的 Azure CDN 标准版**
    * **来自 Verizon 的高级 Azure CDN**

3. 在 CDN 终结点列表中，选择包含自定义域的终结点。

    ![终结点列表](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    此时会显示“终结点”页。 

4. 在自定义域列表中，选择要为其启用 HTTPS 的自定义域。

    ![屏幕截图显示了“自定义域”页，其中包含“使用我自己的证书”选项。](./media/cdn-custom-ssl/cdn-custom-domain.png)

    此时将显示“自定义域”页。 

5. 在证书管理类型下，选择“CDN 托管”  。

6. 选择“打开”，启用 HTTPS  。

    ![自定义域 HTTPS 状态](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. 继续[验证域](#validate-the-domain)。


# <a name="option-2-enable-https-with-your-own-certificate"></a>[选项 2：使用自己的证书启用 HTTPS](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> 此选项仅适用于来自 Microsoft 的 Azure CDN 和来自 Verizon 的 Azure CDN 配置文件   。 
>
 
可以使用自己的证书启用 HTTPS 功能。 此过程通过与 Azure Key Vault 的集成完成，后者允许你安全地存储证书。 Azure CDN 使用此安全机制来获得你的证书，因此需要额外一些步骤。 创建 TLS/SSL 证书时，必须使用允许的证书颁发机构 (CA) 创建。 否则，如果使用不允许的 CA，你的请求将被拒绝。 有关允许的 CA 的列表，请参阅[允许在 Azure CDN 上启用自定义 HTTPS 的证书颁发机构](cdn-troubleshoot-allowed-ca.md)。 对于“Verizon 的 Azure CDN”，将接受任何有效的 CA。 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>准备 Azure Key Vault 帐户和证书
 
1. Azure Key Vault：在要启用自定义 HTTPS 的 Azure CDN 配置文件和 CDN 终结点的同一订阅下，必须具有正在运行的 Azure Key Vault 帐户。 创建 Azure Key Vault 帐户（如果还没有帐户）。
 
2. Azure Key Vault 证书：如果你有证书，请直接将其上传到 Azure Key Vault 帐户。 如果没有证书，请通过 Azure Key Vault 直接创建新证书。

### <a name="register-azure-cdn"></a>注册 Azure CDN

通过 PowerShell 将 Azure CDN 注册为 Azure Active Directory 中的应用。

1. 根据需要在本地计算机上安装 [Azure PowerShell](/powershell/azure/install-az-ps)。

2. 在 PowerShell 中运行以下命令：

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8 是适用于 Microsoft.AzureFrontDoor-Cdn 的服务主体 。

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>向 Azure CDN 授予 Key Vault 的访问权限
 
在 Azure Key Vault 帐户中为 Azure CDN 授予证书（机密）的访问权限。

1. 在 Key Vault 的“设置”部分，选择“访问策略” 。 在右窗格中，选择“+添加访问策略”：

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="为 CDN 创建 keyvault 访问策略" border="true":::

2. 在“添加访问策略”页面中，勾选“选择主体”旁边的“未选中”  。 在“主体”页面中，输入“205478c0-bd83-4e1b-a9d6-db63a3e1e1c8” 。 选择“Microsoft.AzureFrontdoor-Cdn”。  勾选“选择”：

2. 在“选择主体”中，搜索“205478c0-bd83-4e1b-a9d6-db63a3e1e1c8”，并选择“Microsoft.AzureFrontDoor-Cdn”  。 选择“选择”  。

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="选择 Azure CDN 的服务主体" border="true":::
    
3. 选择“证书权限”。 选中“Get”和“List”复选框，以允许 CDN 权限获取和列出证书 。

4. 选择“机密权限”。 选中“Get”和“List”复选框，以允许 CDN 权限获取和列出机密 ：

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="为 CDN 选择 keyvault 的权限" border="true":::

5. 选择 **添加** 。 

    Azure CDN 现在可以访问此 Key Vault 和存储在其中的证书（机密）。
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>选择要部署的 Azure CDN 证书
 
1. 返回 Azure CDN 门户，选择想要启用自定义 HTTPS 的配置文件和 CDN 终结点。 

2. 在自定义域列表中，选择要为其启用 HTTPS 的自定义域。

    此时将显示“自定义域”页。 

3. 在证书管理类型下，选择“使用我自己的证书”  。 

    ![配置证书](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. 选择 Key Vault，证书（机密）和证书版本。

    Azure CDN 会列出下列信息： 
    - 订阅 ID 的 Key Vault 帐户。 
    - 所选 Key Vault 下的证书（机密）。 
    - 可用证书版本。 
 
5. 选择“打开”，启用 HTTPS  。
  
6. 使用你的证书时，无需验证域。 转到[等待传播](#wait-for-propagation)。

---

## <a name="validate-the-domain"></a>验证域

如果你有一个自定义域正在使用中，且它已映射到具有 CNAME 记录的自定义终结点，或者你使用的是自己的证书，请转到[映射到 CDN 终结点的自定义域](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)。 

如果终结点的 CNAME 记录条目不复存在，或者它包含 cdnverify 子域，请转到[未映射到 CDN 终结点的自定义域](#custom-domain-isnt-mapped-to-your-cdn-endpoint)。

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>自定义域已通过 CNAME 记录映射到 CDN 终结点

将自定义域添加到终结点时，你在 DNS 域注册器中创建了一个映射到 CDN 终结点主机名的 CNAME 记录。 

如果该 CNAME 记录仍然存在，并且不包含 cdnverify 子域，则 DigiCert CA 将使用它来自动验证自定义域的所有权。 

如果使用的是自己的证书，则无需验证域。

CNAME 记录应采用以下格式：

* “名称”是指你的自定义域名。
* “值”是指 CDN 终结点主机名。

| 名称            | 类型  | 值                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

有关 CNAME 记录的详细信息，请参阅[创建 CNAME DNS 记录](./cdn-map-content-to-custom-domain.md)。

如果 CNAME 记录采用正确的格式，DigiCert 会自动验证自定义域名，并为域创建证书。 DigitCert 不会向你发送验证电子邮件，并且你无需批准请求。 该证书会在一年内有效，并会在过期前自动续订。 转到[等待传播](#wait-for-propagation)。 

自动验证通常需要几个小时。 如果在 24 小时内未看到域完成验证，请创建一个支持票证。

>[!NOTE]
>如果通过 DNS 提供商获得证书颁发机构授权 (CAA) 记录，则必须包含 DigiCert 作为一个有效的 CA。 CAA 记录允许域名所有者通过自己的 DNS 提供商指定哪些 CA 有权为其域名颁发证书。 如果某个 CA 收到具有 CAA 记录的域证书订单，但该 CA 未被列为授权的颁发者，则禁止向该域或子域颁发证书。 有关管理 CAA 记录的信息，请参阅[管理 CAA 记录](https://support.dnsimple.com/articles/manage-caa-record/)。 有关 CAA 记录工具，请参阅 [CAA 记录帮助器](https://sslmate.com/caa/)。

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>未映射到 CDN 终结点的自定义域

>[!NOTE]
>如果使用的是 **Akamai 的 Azure CDN**，则应设置以下 CNAME 以启用自动域验证。 "_acme-challenge.&lt;custom domain hostname&gt; -> CNAME -> &lt;custom domain hostname&gt;.ak-acme-challenge.azureedge.net"

如果 CNAME 记录条目包含 cdnverify 子域，请按照此步骤中的其余说明进行操作。

DigiCert 会向以下电子邮件地址发送验证电子邮件。 请验证是否可直接从以下地址之一进行审批：

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

你会在几分钟内收到一封电子邮件，要求你批准请求。 如果正在使用垃圾邮件筛选器，请将 verification@digicert.com 添加到其允许列表。 如果未在 24 小时内收到电子邮件，请与 Microsoft 支持部门联系。
    
![域验证电子邮件](./media/cdn-custom-ssl/domain-validation-email.png)

选择批准链接时，会转到以下在线批准表： 
    
![域验证表单](./media/cdn-custom-ssl/domain-validation-form.png)

按表中的说明操作；有两种验证选项：

- 可批准通过同一根域（例如 consoto.com）的同一帐户下的所有将来订单。 如果你打算为同一根域添加其他自定义域，建议使用此方法。

- 可以只批准该请求中使用的特定主机名。 需要对后续请求另外进行审批。

批准后，DigiCert 会针对自定义域名完成证书创建。 该证书会在一年内有效，并会在过期前自动续订。

## <a name="wait-for-propagation"></a>等待传播

验证域名后，将需要长达 6-8 小时才能使自定义域 HTTPS 功能激活。 当进程完成时，Azure 门户中的自定义 HTTPS 状态将更改为“已启用”。 “自定义域”对话框中的 4 个操作步骤将标记为“已完成”。 自定义域现可使用 HTTPS。

![启用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作进度

下表显示启用 HTTPS 时出现的操作进度。 启用 HTTPS 后，自定义域对话框中将出现四个操作步骤。 当每个步骤变为活动状态时，其下将随之显示其他子步骤详细信息。 并非所有这些子步骤都会执行。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作步骤 | 操作子步骤详细信息 | 
| --- | --- |
| 1 提交请求 | 提交请求 |
| | 正在提交 HTTPS 请求。 |
| | 已成功提交 HTTPS 请求。 |
| 2 域验证 | 如果域是映射到 CDN 终结点的 CNAME，则会自动验证域。 否则，将会向域的注册记录中列出的电子邮件（WHOIS 注册者）发送一个验证请求。|
| | 已成功验证域所有权。 |
| | 域所有权验证请求已过期（很可能是客户在 6 天内未响应）。 将不会在域中启用 HTTPS。 * |
| | 客户已拒绝域所有权验证请求。 将不会在域中启用 HTTPS。 * |
| 3 证书预配 | 证书颁发机构当前正在颁发在你的域中启用 HTTPS 所需的证书。 |
| | 证书已颁发，当前正将证书部署到 CDN 网络。 最多可能需要 6 小时才能完成此操作。 |
| | 已成功将证书部署到 CDN 网络。 |
| 4 完成 | 已成功在域中启用 HTTPS。 |

\* 除非出现错误，否则不会显示此消息。 

如果提交请求之前出现错误，则会显示以下错误消息：

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>清理资源 - 禁用 HTTPS

本部分介绍了如何为自定义域禁用 HTTPS。

### <a name="disable-the-https-feature"></a>禁用 HTTPS 功能 

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“CDN 配置文件”。  

2. 选择“Microsoft 的 Azure CDN 标准版”、“Verizon 的 Azure CDN 标准版”或“Verizon 的 Azure CDN 高级版”配置文件    。

3. 在终结点的列表中，选取包含自定义域的终结点。

4. 选择要禁用 HTTPS 的自定义域。

    ![自定义域列表](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. 选择“禁用”  以禁用 HTTPS，然后选择“应用”  。

    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>等待传播

禁用自定义域 HTTPS 功能后，最多可能需要 6-8 小时才会生效。 当进程完成时，Azure 门户中的自定义 HTTPS 状态将更改为“已禁用”。 “自定义域”对话框中的 3 个操作步骤将标记为“已完成”。 自定义域不再能够使用 HTTPS。

![禁用 HTTPS 对话框](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>操作进度

下表显示在禁用 HTTPS 时发生的操作进程。 禁用 HTTPS 后，“自定义域”对话框中将出现 3 个操作步骤。 当某个步骤变为活动状态时，该步骤下会显示详细信息。 步骤成功完成后，它旁边会显示一个绿色的复选标记。 

| 操作进度 | 操作详细信息 | 
| --- | --- |
| 1 提交请求 | 提交请求 |
| 2 证书取消预配 | 删除证书 |
| 3 完成 | 已删除证书 |

## <a name="frequently-asked-questions"></a>常见问题

1. 谁是证书提供者？使用哪种类型的证书？ 

    Digicert 提供的专用证书用于自定义域：
    
    * **Verizon 的 Azure CDN**
    * **Microsoft 的 Azure CDN**

2. 使用基于 IP 的 TLS/SSL 还是 SNI TLS/SSL？ 

    **Verizon 的 Azure CDN** 和 **Microsoft 的 Azure CDN Standard** 都使用 SNI TLS/SSL。

3. 如果我未收到 DigiCert 发来的域验证电子邮件，怎么办？ 

    如果你未在使用 cdnverify 子域，并且 CNAME 条目指向终结点主机名，那么你将不会收到域验证电子邮件。
     
    验证会自动进行。 否则，如果你没有 CNAME 条目，并且在 24 小时内未收到电子邮件，请联系 Microsoft 支持部门。

4. 使用 SAN 证书是否没有使用专用证书安全？ 
    
    SAN 证书遵循与专用证书相同的加密和安全标准。 所有颁发的 TLS/SSL 证书都使用 SHA-256 来增强服务器安全性。

5. 我是否需要通过我的 DNS 提供商获得证书颁发机构授权记录？ 

    当前无需具备证书颁发机构授权记录。 但是，如果你确实有一个，则必须包含 DigiCert 作为一个有效的 CA。

6. 从 2018 年 6 月 20 日开始，Verizon 的 Azure CDN 默认使用专用证书和 SNI TLS/SSL。  使用“使用者可选名称”(SAN) 证书和基于 IP 的 TLS/SSL 的现有自定义域会发生什么情况？

    如果 Microsoft 经分析后发现，只是向应用程序发出了仅 SNI 客户端请求，则现有的域将在未来几个月逐渐迁移到单个证书。 
    
    如果检测到非 SNI 客户端，则域将保留在具有基于 IP 的 TLS/SSL 的 SAN 证书中。 对非 SNI 的服务或客户端的请求不受影响。

7. *证书续订如何处理自带证书？*

    若要确保将较新的证书部署到 PoP 基础结构，请将新证书上传到 Azure KeyVault。 在 Azure CDN 上的 TLS 设置中，选择最新的证书版本并选择“保存”。 然后 Azure CDN 将传播新的已更新证书。 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> - 在自定义域上启用 HTTPS 协议。
> - 使用 CDN 托管的证书 
> - 使用自己的证书
> - 验证域。
> - 在自定义域上禁用 HTTPS 协议。

继续学习下一教程，了解如何在 CDN 终结点上配置缓存。

> [!div class="nextstepaction"]
> [教程：设置 Azure CDN 缓存规则](cdn-caching-rules-tutorial.md)