---
title: 购买自定义域名
description: 了解如何购买应用服务域，并将其用作应用 Azure App Service 的自定义域。
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 6f0ff7a54c2ad1fa1af649c8082498b442783c7e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607960"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>为 Azure 应用服务购买自定义域名

应用服务域是直接在 Azure 中管理的自定义域。 使用这些域可以轻松管理 [Azure 应用服务](overview.md)的自定义域。 本教程介绍如何购买应用服务域并将 DNS 名称分配到 Azure 应用服务。

对于 Azure VM 或 Azure 存储，请查看[Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)（将应用服务分配到 Azure VM 或 Azure 存储）。 对于云服务，请查看[为 Azure 云服务配置自定义域名](../cloud-services/cloud-services-custom-domain-name-portal.md)。

## <a name="prerequisites"></a>先决条件

完成本教程：

* [创建应用服务应用](./index.yml)，或使用为另一教程创建的应用。 应用应位于 Azure 公共区域中。 目前，不支持 Azure 国内云。
* [移除订阅中的支出限制](../cost-management-billing/manage/spending-limit.md#remove)。 无法购买具有免费订阅信用额度的应用服务域。

## <a name="buy-an-app-service-domain"></a>购买应用服务域

有关应用服务域的定价信息，请访问 [应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/) 并向下滚动到 "应用服务域"。

1. 打开 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

1. 在搜索栏中，搜索并选择 " **应用服务域**"。

    ![门户导航到 Azure App Service 域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在 **应用服务域** 视图中，单击 " **添加**"。

    ![单击 "在应用服务域中添加"](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. 选择 " **单击以尝试更新版本的应用服务域创建体验"**。

    ![创建具有新体验的应用服务域](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>“基本信息”选项卡

1. 在 " **基本** 信息" 选项卡中，使用下表配置设置：  

   | 设置  | 描述 |
   | -------- | ----------- |
   | **订阅** | 用于购买域的订阅。 |
   | **资源组** | 要放置域的资源组。 例如，你的应用所在的资源组。 |
   | **域** | 键入所需的域。 例如， **contoso.com**。 如果所需的域不可用，可以从可用域的建议列表中进行选择，或尝试其他域。 |

    > [!NOTE]
    > 应用服务域支持以下[顶级域](https://wikipedia.org/wiki/Top-level_domain)：com、net、co.uk、org、nl、in、biz、org.uk 和 co.in。
    >
    >
    
2. 完成后，单击 " **下一步：联系人信息**"。

### <a name="contact-information-tab"></a>联系人信息选项卡

1. 为域注册提供 [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) 所需的信息。 

    请务必尽量准确填写所有必填字段。 联系信息的数据不正确可能会导致无法购买域。

1. 完成后，单击 " **下一步：高级**"。

### <a name="advanced-tab"></a>高级选项卡

1. 在 " **高级** " 选项卡中，配置可选设置：  

   | 设置  | 描述 |
   | -------- | ----------- |
   | **自动续订** | 默认情况下启用。 应用服务域会以一年的增量注册到你。 自动续订确保你的域注册不会过期，并且你保留域的所有权。 续订时，Azure 订阅会自动向每年的域注册费用收费。 若要选择退出，请选择 " **禁用**"。 如果禁用自动续订，则可以 [手动续订](#renew-the-domain)。 |
   | **隐私保护** | 默认情况下启用。 隐私保护将从 WHOIS 数据库中隐藏域注册联系信息。 隐私保护已包含在每年的域注册费中。 若要选择退出，请选择 " **禁用**"。 |

2. 完成后，单击 " **下一步：标记**"。

### <a name="finish"></a>完成

1. 在 " **标记** " 选项卡中，为应用服务域设置所需的标记。 使用应用服务域不需要标记，但它是 [Azure 中的一项功能，可帮助你管理资源](../azure-resource-manager/management/tag-resources.md)。

1. 单击 " **下一步"：查看 + 创建**。

1. 在 " **查看** " 和 "创建" 选项卡中，查看域顺序。 完成后，单击“创建”。

    > [!NOTE]
    > 应用服务域使用 GoDaddy 进行域注册，使用 Azure DNS 来托管域。 除每年的域注册费外，Azure DNS 适用的使用费用。 有关信息，请参阅 [Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。
    >
    >

1. 域注册完成后，会看到 " **中转到资源** " 按钮。 选择它以查看其管理页面。

    ![已创建应用服务域。 转到资源](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

你现在已准备好将应用服务应用分配到此自定义域。

## <a name="prepare-the-app"></a>准备应用

若要将自定义 DNS 名称映射到 Web 应用，Web 应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（Azure Functions 的共享、基本、标准、高级或消耗层）。 在此步骤中，需确保应用服务计划位于受支持的定价层。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>导航到 Azure 门户中的应用

1. 在顶部搜索栏中，搜索并选择 " **应用服务**"。

    ![搜索应用服务](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 选择应用程序的名称。

    ![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

    将看到应用服务应用的管理页。  

### <a name="check-the-pricing-tier"></a>检查定价层

1. 在应用页的左侧导航窗格中，向下滚动到“设置”部分，然后选择“增加(应用服务计划)”。

    ![扩展菜单](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 蓝色边框突出显示了应用的当前层。 检查以确保应用不在 **F1** 层中。 **F1** 层不支持自定义 DNS。 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text=") 选择 (应用服务计划的应用页面左侧导航菜单的屏幕截图。":::

1. 如果应用服务计划不在 **F1** 层中，请关闭“纵向扩展”页并跳转到 [购买域](#buy-an-app-service-domain)。

### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

1. 选择任何非免费层（**D1**、**B1**、**B2**、**B3**，或“生产”类别中的任何层）。 有关其他选项，请单击“查看其他选项”。

1. 单击“应用” 。

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="生产类别中的自定义域定价层的屏幕截图，其中突出显示了 &quot;生产&quot; 选项卡、B1 计划和 &quot;应用&quot; 按钮。":::

    看到以下通知时，说明缩放操作已完成。

    ![缩放操作确认](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>将应用服务域映射到你的应用

可以轻松地将应用服务域中的主机名映射到应用服务应用，只要它在同一订阅中即可。 直接在应用中映射应用服务域或其任何子域，Azure 将为你创建所需的 DNS 记录。

> [!NOTE]
> 如果域和应用在不同的订阅中，则可以将应用服务域映射到应用，就像 [映射外部购买的域](app-service-web-tutorial-custom-domain.md)一样。 在这种情况下，Azure DNS 是外部域提供程序，需要 [手动添加所需的 DNS 记录](#manage-custom-dns-records)。
>

### <a name="map-the-domain"></a>映射域

1. 在应用页面的左侧导航栏中，滚动到 " **设置** " 部分，并选择 " **自定义域**"。

    ![显示“自定义域”菜单的屏幕截图。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 选择“添加自定义域”。

    ![显示“添加主机名”项的屏幕截图。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 键入应用服务域 (例如 **contoso.com**) 或子域 (如 **www.contoso.com**) ，然后单击 " **验证**"。

    > [!NOTE]
    > 如果在应用服务域名中进行了键入错误，则会在页面底部显示验证错误，告诉你缺少某些 DNS 记录。 无需手动添加应用服务域的这些记录。 只需确保正确键入域名，再单击 " **验证** "。
    >
    > ![显示验证错误的屏幕截图。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. 接受 **Hostname 记录类型** ，然后单击 " **添加自定义域**"。

    ![显示“添加自定义域”按钮的屏幕截图。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. 新的自定义域可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。

    ![演示如何添加 CNAME 记录的屏幕截图。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 自定义域的 " **不安全** " 标签表示该标签尚未绑定到 TLS/SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告，具体取决于浏览器。 若要添加 TLS 绑定，请参阅[在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)。
    
### <a name="test-the-custom-domain"></a>测试自定义域

若要测试自定义域，请在浏览器中导航到该自定义域。

## <a name="renew-the-domain"></a>续订域

你所购买的应用服务域自购买之日起的一年内有效。 默认情况下，通过收取下一年费用的付款方式将域配置为自动续订。 你可以手动续订你的域名。

如果要关闭自动续订，或者手动续订域，请按照以下步骤操作。

1. 在搜索栏中，搜索并选择 " **应用服务域**"。

    ![门户导航到 Azure App Service 域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在“应用服务域”部分，选择要配置的域。

1. 从域的左侧导航窗格，选择“域续订”。 若要停止自动续订域，请选择 " **关闭**"。 该设置将立即生效。

    ![显示自动续订你的域的选项的屏幕截图。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > 离开页面时，单击 **"确定**" 将忽略 "未保存的编辑将被丢弃" 错误。
    >

若要手动续订域，选择“续订域”。 但是，此按钮在 [域过期之前90天内](#when-domain-expires)处于非活动状态。

如果域续订成功，则会在24小时内收到电子邮件通知。

## <a name="when-domain-expires"></a>域过期时间

Azure 会处理过期或过期的应用服务域，如下所示：

* 如果禁用自动续订：在域过期之前90天，将向你发送续订通知电子邮件，并在门户中激活 " **续订域** " 按钮。
* 如果启用自动续订：在你的域到期日期之后的那一日，Azure 将尝试为你的域名续订付费。
* 如果在自动续订期间出现错误 (例如，文件上的卡已过期) ，或者禁用了自动续订，并且你允许域过期，则 Azure 会通知你域过期，并使你的域名过期。 你可以 [手动续订](#renew-the-domain) 你的域。
* 在过期后的第4天和第12天，Azure 会向你发送其他通知电子邮件。 你可以 [手动续订](#renew-the-domain) 你的域。
* 在过期后的第19天，你的域仍保持不变，但会收取兑换费。 你可以致电客户支持人员续订你的域名，但需遵守任何适用的续订和兑换费用。
* 在过期后的第25天，Azure 将使用域名行业拍卖服务使你的域与拍卖联系。 你可以致电客户支持人员续订你的域名，但需遵守任何适用的续订和兑换费用。
* 过期后的30天后，你将无法再兑换你的域。

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>管理自定义 DNS 记录

在 Azure 中，可以使用 [Azure DNS](https://azure.microsoft.com/services/dns/) 管理应用服务域的 DNS 记录。 可以添加、删除和更新 DNS 记录，就像使用外部购买的域时一样。 管理自定义 DNS 记录：

1. 在搜索栏中，搜索并选择 " **应用服务域**"。

    ![门户导航到 Azure App Service 域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在“应用服务域”部分，选择要配置的域。

1. 从 " **概述** " 页中，选择 " **管理 DNS 记录**"。

    ![显示 DNS 记录访问位置的屏幕截图。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

有关如何编辑 DNS 记录的信息，请参阅[如何在 Azure 门户中管理 DNS 区域](../dns/dns-operations-dnszones-portal.md)。

## <a name="cancel-purchase-delete-domain"></a>取消购买（删除域）

购买应用服务域后，可在五天内取消购买以获得全额退款。 五天之后，可以删除应用服务域，但无法获得退款。

1. 在搜索栏中，搜索并选择 " **应用服务域**"。

    ![门户导航到 Azure App Service 域](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. 在“应用服务域”部分，选择要配置的域。

1. 在域的左侧导航窗格中，选择 " **主机名绑定**"。 此处列出了所有 Azure 服务的主机名绑定。

    ![显示 "主机名绑定" 页的屏幕截图。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. 通过 **选择**  >  "删除每个主机名"**删除**。 删除所有绑定后，选择“保存”。

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. 在域的左侧导航窗格中，选择 " **概述**"。 

1. 如果所购买域的取消期限未过，请选择“取消购买”。 否则，请参阅“删除”按钮。 若要删除该域且不想要获得退款，请选择“删除”。

    ![显示在何处删除或取消购买的域的屏幕截图。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. 通过选择 **"是"** 确认操作。

    完成该操作后，该域将从订阅中释放，可供任何人再次购买。 

## <a name="direct-default-url-to-a-custom-directory"></a>将默认 URL 定向到自定义目录

默认情况下，应用服务将 Web 请求定向到应用代码的根目录下。 若要将它们定向到一个子目录（如 `public` ），请参阅 [重定向到自定义目录](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)。

## <a name="next-steps"></a>后续步骤

了解如何将自定义 TLS/SSL 证书绑定到应用服务。

> [!div class="nextstepaction"]
> [在 Azure 应用服务中使用 TLS 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
