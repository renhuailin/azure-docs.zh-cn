---
title: 在 Azure 前门标准/高级 SKU 配置中为自定义域配置 HTTPS
description: 本文介绍如何将自定义域加入 Azure 前门标准/高级 SKU。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740811"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>在前门标准/高级 SKU 上配置 HTTPS (使用 Azure 门户预览) 自定义域

> [!NOTE]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

默认情况下，Azure 前门标准/高级版允许在添加自定义域时，将安全 TLS 传递到应用程序。 通过在自定义域上使用 HTTPS 协议，可以确保在 internet 上发送敏感数据时，使用 TLS/SSL 加密来安全地提供敏感数据。 Web 浏览器通过 HTTPS 连接到网站时，它会验证网站的安全证书并验证该证书是否是由合法的证书颁发机构颁发的。 此过程提供安全性并保护 Web 应用程序免受攻击。

Azure 前门标准/高级版支持 Azure 托管证书和客户管理的证书。 默认情况下，azure 前门会自动使用 Azure 托管证书为所有自定义域启用 HTTPS。 获取 Azure 托管证书不需要执行其他步骤。 证书是在域验证过程中创建的。 还可以通过将 Azure 前门标准/高级版与 Key Vault 集成来使用自己的证书。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* 必须先创建 Azure 前门标准/高级配置文件，然后才能配置自定义域的 HTTPS。 有关详细信息，请参阅 [快速入门：创建 Azure 前门标准/高级配置文件](create-front-door-portal.md)。

* 如果还没有自定义域，则必须先使用域提供商购买一个。 有关示例，请参阅[购买自定义域名](../../app-service/manage-custom-dns-buy-domain.md)。

* 如果要使用 Azure 来托管 [DNS 域](../../dns/dns-overview.md)，则必须将域提供商的域名系统 (DNS) 委托给 Azure DNS。 有关详细信息，请参阅 [向 Azure DNS 委派域](../../dns/dns-delegate-domain-azure-dns.md)。 否则，如果你使用域提供程序来处理 DNS 域，则必须通过输入提示的 DNS TXT 记录手动验证域。

## <a name="azure-managed-certificates"></a>Azure 托管证书

1. 在 Azure 前门标准/高级配置文件的 "设置" 下，选择 " **域** "，然后选择 " **+ 添加** " 以添加新域。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="域配置登录页的屏幕截图。":::

1. 在 " **添加域** " 页上，对于 " *DNS 管理* "，选择 " **Azure 托管 DNS** " 选项。 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="选择 &quot;添加 Azure 托管 DNS 的域&quot; 页面的屏幕截图。":::

1. 按照启用 [自](how-to-add-custom-domain.md)定义域中的步骤操作，验证自定义域并将其与终结点相关联。

1. 成功将自定义域关联到终结点后，会将 Azure 托管证书部署到前门。 此过程可能需要几分钟才能完成。

## <a name="using-your-own-certificate"></a>使用自己的证书

还可以选择使用自己的 TLS 证书。 必须先将此证书导入到 Azure Key Vault，然后才能将其与 Azure 前门标准/高级版结合使用。 请参阅将 [证书导入](../../key-vault/certificates/tutorial-import-certificate.md) Azure Key Vault。 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>准备 Azure Key Vault 帐户和证书
 
1. 你必须在与你要启用自定义 HTTPS 的 Azure 前门标准/高级版相同的订阅下运行 Azure Key Vault 帐户。 创建 Azure Key Vault 帐户（如果还没有帐户）。

    > [!WARNING]
    > Azure Front Door 目前仅支持 Front Door 配置所在的同一订阅中的 Key Vault 帐户。 选择与 Azure 前门标准/高级版不同的订阅下的 Key Vault 将导致失败。

1. 如果已有证书，可以将其直接上传到 Azure Key Vault 帐户。 否则，请直接通过 Azure Key Vault 与集成的某个伙伴证书颁发机构 Azure Key Vault 创建新证书。 将证书上传为 **证书** 对象，而不是 **机密**。

    > [!NOTE]
    > 对于你自己的 TLS/SSL 证书，Front Door 不支持带有 EC 加密算法的证书。

#### <a name="register-azure-front-door"></a>注册 Azure Front Door

通过 PowerShell 将 Azure Front Door 的服务主体注册为 Azure Active Directory 中的应用。

> [!NOTE]
> 此操作需要全局管理员权限，并且每个租户只需要执行 **一次**。

1. 根据需要在本地计算机上的 PowerShell 中安装 [Azure PowerShell](/powershell/azure/install-az-ps)。

1. 在 PowerShell 中运行以下命令：

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>授予 Azure Front Door 对 Key Vault 的访问权限
 
授予 Azure Front Door 访问 Azure Key Vault 帐户中的证书的权限。

1. 在密钥保管库帐户的 "设置" 下，选择 " **访问策略**"。 然后选择 "添加" " **新建** " 以创建新策略。

1. 在 " **选择主体**" 中搜索 " **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**"，并选择 "AzureFrontDoor-Cdn"。 单击“选择”  。

1. 在“机密权限”  中，选择 **Get** 以允许 Front Door 检索证书。

1. 在“证书权限”  中，选择 **Get** 以允许 Front Door 检索证书。

1. 选择“确定”  。 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>选择要部署的 Azure Front Door 的证书
 
1. 返回到门户中的 Azure 前门标准/高级版。

1. 导航到 "*设置*" 下的 **机密**，然后选择 "**添加证书**"。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Azure 前门机密登录页的屏幕截图。":::

1. 在 " **添加证书** " 页上，选中要添加到 Azure 前门标准/高级版的证书对应的复选框。 将版本选择保留为 "最新"，并选择 " **添加**"。 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="&quot;添加证书&quot; 页的屏幕截图。":::

1. 成功设置证书后，可以在添加新的自定义域时使用该证书。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="已成功将证书添加到机密的屏幕截图。":::

1. 导航到 "*设置*" 下的 "**域**"，然后选择 " **+ 添加**" 以添加新的自定义域。 在 " **添加域** " 页面上，选择 "为 *HTTPS* (BYOC) "。 对于 " *机密*"，请从下拉端中选择要使用的证书。 

    > [!NOTE]
    > 所选证书的名称必须与要添加的自定义域 (CN) 相同。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="使用 HTTPS 添加自定义域页的屏幕截图。":::

1. 按照屏幕上的步骤验证证书。 然后，按照 [创建自](how-to-add-custom-domain.md) 定义域指南中所述将新创建的自定义域与终结点相关联。

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>从 Azure 托管更改为自带证书 (BYOC) 

1. 可以通过选择证书状态以打开 " **证书详细信息** " 页，将现有 Azure 托管证书更改为用户管理的证书。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="域登录页上的证书状态的屏幕截图。" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. 在 " **证书详细信息** " 页上，你可以从 "Azure 托管" 更改为 "自带证书 (BYOC) " 选项。 然后执行与前面相同的步骤来选择证书。 选择 " **更新** " 以更改与域关联的证书。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="证书详细信息页的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解 [Azure 前门标准/高级版的缓存](concept-caching.md)。
