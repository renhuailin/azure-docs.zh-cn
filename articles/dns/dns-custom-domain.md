---
title: 将 Azure DNS 与 Azure 资源集成 - Azure DNS
description: 本文介绍如何使用 Azure DNS 为 Azure 资源提供 DNS。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/06/2021
ms.author: rohink
ms.openlocfilehash: c09c1840c9976a2ac14cb5ef73f8c4e15000c54f
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634847"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>使用 Azure DNS 为 Azure 服务提供自定义域设置

Azure DNS 为支持自定义域或具有完全限定域名 (FQDN) 的任何 Azure 资源提供命名解析。 例如，你有一个 Azure Web 应用，并且你希望用户使用 `contoso.com` 或 `www.contoso.com` 作为 FQDN 对其进行访问。 本文逐步讲解如何使用 Azure DNS 配置 Azure 服务，以便使用自定义域。

## <a name="prerequisites"></a>必备条件

若要对自定义域使用 Azure DNS，首先必须将该域委托给 Azure DNS。 有关如何为委托配置名称服务器的说明，请参阅[将域委托给 Azure DNS](./dns-delegate-domain-azure-dns.md)。 将域委托给 Azure DNS 区域后，便可以配置所需的 DNS 记录。

可为 Azure 函数应用、公共 IP 地址、应用服务（Web 应用）、Blob 存储和 Azure CDN 配置虚构域或自定义域。

## <a name="azure-function-app"></a>Azure 函数应用

若要为 Azure 函数应用配置自定义域，需要创建一条 CNAME 记录，并在该函数应用本身上进行配置。
 
1. 导航到“函数应用”并选择自己的函数应用。  选择“设置”下的“自定义域”。 记下“分配的自定义域”下的当前 URL，此地址用作创建的 DNS 记录的别名。

    :::image type="content" source="./media/dns-custom-domain/function-app.png" alt-text="函数应用的自定义域的屏幕截图。":::

1. 导航到 DNS 区域，选择“+ 记录集”。 在“添加记录集”页上输入以下信息，选择“确定”创建该记录集 。

    :::image type="content" source="./media/dns-custom-domain/function-app-record.png" alt-text="函数应用“添加记录集”页面的屏幕截图。":::

    |属性  |值  |说明  |
    |---------|---------|---------|
    | 名称 | myfunctionapp | 此值连同域名标签是自定义域名的 FQDN。 |
    | 类型 | CNAME | 使用 CNAME 记录相当于使用别名。 |
    | TTL | 1 | 1 表示 1 小时  |
    | TTL 单位 | 小时 | 小时用作时间计量单位  |
    | Alias | contosofunction.azurewebsites.net | 为其创建别名的 DNS 名称，在本示例中，为默认提供给函数应用的 contosofunction.azurewebsites.net DNS 名称。        |
    
1. 导航回函数应用，选择“设置”下的“自定义域”。 然后，选择“+ 添加自定义域”。

    :::image type="content" source="./media/dns-custom-domain/function-app-add-domain.png" alt-text="函数应用“添加自定义域”按钮的屏幕截图。":::    

1. 在“添加自定义域”页的“自定义域”文本字段中，输入 CNAME 记录并选择“验证”  。 如果找到该记录，则会出现“添加自定义域”按钮。 选择“添加自定义域”，添加别名。

    :::image type="content" source="./media/dns-custom-domain/function-app-cname.png" alt-text="函数应用“添加自定义域”页面的屏幕截图。":::

## <a name="public-ip-address"></a>公共 IP 地址

若要为使用公共 IP 地址资源（例如应用程序网关、负载均衡器、云服务、资源管理器 VM 和经典 VM）的服务配置自定义域，请使用 A 记录。

1. 导航到公共 IP 资源并选择“配置”。 记下显示的 IP 地址。

    :::image type="content" source="./media/dns-custom-domain/public-ip.png" alt-text="公共 IP“配置”页面的屏幕截图。":::

1. 导航到 DNS 区域，选择“+ 记录集”。 在“添加记录集”页上输入以下信息，选择“确定”创建该记录集 。

    :::image type="content" source="./media/dns-custom-domain/public-ip-record.png" alt-text="公共 IP“记录集”页面的屏幕截图。":::

    | 属性 | 值 | 说明 |
    | -------- | ----- | ------------|
    | 名称 | webserver1 | 此值连同域名标签是自定义域名的 FQDN。 |
    | 类型 | A | 由于资源是 IP 地址，因此应使用 A 记录。 |
    | TTL | 1 | 1 表示 1 小时 |
    | TTL 单位 | 小时 | 小时用作时间计量单位 |
    | IP 地址 | `<your ip address>` | 公共 IP 地址。 |

1. 创建 A 记录后，运行 `nslookup` 来验证记录解析。

    :::image type="content" source="./media/dns-custom-domain/public-ip-nslookup.png" alt-text="cmd 中公共 IP 的 nslookup 的屏幕截图。":::

## <a name="app-service-web-apps"></a>应用服务（Web 应用）

下面逐步介绍如何配置应用服务 Web 应用的自定义域。

1. 导航到“应用服务”，选择要配置自定义域名的资源，然后选择“设置”下的“自定义域” 。 记下“分配的自定义域”下的当前 URL，此地址用作创建的 DNS 记录的别名。

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Web 应用的自定义域的屏幕截图。":::

1. 导航到 DNS 区域，选择“+ 记录集”。 在“添加记录集”页上输入以下信息，选择“确定”创建该记录集 。

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Web 应用“记录集”页面的屏幕截图。":::

    | 属性  | 值 | 说明 |
    |---------- | ----- | ----------- |
    | 名称 | mywebserver | 此值连同域名标签是自定义域名的 FQDN。 |
    | 类型 | CNAME | 使用 CNAME 记录相当于使用别名。 如果资源使用了 IP 地址，则会使用 A 记录。 |
    | TTL | 1 | 1 表示 1 小时 |
    | TTL 单位 | 小时 | 小时用作时间计量单位 |
    | Alias | contoso.azurewebsites.net | 为其创建别名的 DNS 名称，在本示例中，为默认提供给 Web 应用的 contoso.azurewebsites.net DNS 名称。 |

1. 导航回 Web 应用，选择“设置”下的“自定义域”。 然后，选择“+ 添加自定义域”。

    :::image type="content" source="./media/dns-custom-domain/web-app-add-domain.png" alt-text="Web 应用“添加自定义域”按钮的屏幕截图。":::  

1. 在“添加自定义域”页的“自定义域”文本字段中，输入 CNAME 记录并选择“验证”  。 如果找到该记录，则会出现“添加自定义域”按钮。 选择“添加自定义域”，添加别名。

    :::image type="content" source="./media/dns-custom-domain/web-app-cname.png" alt-text="Web 应用“添加自定义域”页面的屏幕截图。":::

1. 完成此过程后，请运行 **nslookup** 来验证名称解析是否正常工作。

    :::image type="content" source="./media/dns-custom-domain/app-service-nslookup.png" alt-text="Web 应用的 nslookup 的屏幕截图。"::: 

若要详细了解如何将自定义域映射到应用服务，请访问[将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)。

若要了解如何迁移活动 DNS 名称，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](../app-service/manage-custom-dns-migrate-domain.md)。

如果需要购买应用服务的自定义域，请参阅[购买 Azure Web 应用的自定义域名](../app-service/manage-custom-dns-buy-domain.md)。

## <a name="blob-storage"></a>Blob 存储

下面逐步介绍如何使用 asverify 方法配置 Blob 存储帐户的 CNAME 记录。 此方法可确保不会造成停机。

1. 导航到“存储帐户”，选择存储帐户，然后选择“设置”下的“网络” 。 然后，选择“自定义域”选项卡。记下步骤 2 中的 FQDN，此名称用于创建第一个 CNAME 记录。

    :::image type="content" source="./media/dns-custom-domain/blob-storage.png" alt-text="存储帐户的自定义域的屏幕截图。":::

1. 导航到 DNS 区域，选择“+ 记录集”。 在“添加记录集”页上输入以下信息，选择“确定”创建该记录集 。

    :::image type="content" source="./media/dns-custom-domain/storage-account-record.png" alt-text="存储帐户“记录集”页面的屏幕截图。":::

    | 属性 | 值 | 说明 |
    | -------- | ----- | ----------- |
    | 名称 | asverify.mystorageaccount | 此值连同域名标签是自定义域名的 FQDN。 |
    | 类型 | CNAME | 使用 CNAME 记录相当于使用别名。 |
    | TTL | 1 | 1 表示 1 小时 |
    | TTL 单位 | 小时 | 小时用作时间计量单位 |
    | Alias | asverify.contoso.blob.core.windows.net | 为其创建别名的 DNS 名称，在本示例中，为默认提供给存储帐户的 asverify.contoso.blob.core.windows.net DNS 名称。 |

1. 导航回存储帐户并选择“网络”，然后选择“自定义域”选项卡。在文本框中键入创建的别名，但不含 asverify 前缀，勾选“使用间接 CNAME 验证”，然后选择“保存”   。 

    :::image type="content" source="./media/dns-custom-domain/blob-storage-add-domain.png" alt-text="存储帐户“添加自定义域”页面的屏幕截图。":::

1. 返回 DNS 区域，创建 CNAME 记录，但不含 asverify 前缀。  此后，可以安全删除带有 asverify 前缀的 CNAME 记录。

    :::image type="content" source="./media/dns-custom-domain/storage-account-record-set.png" alt-text="不含 asverify 前缀的存储帐户记录的屏幕截图。":::

1. 运行 `nslookup` 验证 DNS 解析。

若要详细了解如何将自定义域映射到 Blob 存储终结点，请访问[为 Blob 存储终结点配置自定义域名](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

下面逐步介绍如何使用 cdnverify 方法配置 CDN 终结点的 CNAME 记录。 此方法可确保不会造成停机。

1. 导航到 CDN 配置文件并选择正在使用的终结点。 选择“+ 自定义域”。 记下“终结点主机名”，因为此值是该 CNAME 记录指向的记录。

    :::image type="content" source="./media/dns-custom-domain/cdn.png" alt-text="CDN“自定义域”页面的屏幕截图。":::

1. 导航到 DNS 区域，选择“+ 记录集”。 在“添加记录集”页上输入以下信息，选择“确定”创建该记录集 。

    :::image type="content" source="./media/dns-custom-domain/cdn-record.png" alt-text="CDN“记录集”页面的屏幕截图。":::

    | 属性 | 值 | 说明 |
    | -------- | ----- | ----------- |
    | 名称 | cdnverify.mycdnendpoint | 此值连同域名标签是自定义域名的 FQDN。 |
    | 类型 | CNAME | 使用 CNAME 记录相当于使用别名。 |
    | TTL | 1 | 1 表示 1 小时 |
    | TTL 单位 | 小时 | 小时用作时间计量单位 |
    | Alias | cdnverify.contoso.azureedge.net | 为其创建别名的 DNS 名称，在本示例中，为默认提供给 CDN 终结点的 cdnverify.contoso.azureedge.net DNS 名称。 |

1. 导航回 CDN 终结点并选择“+ 自定义域”。 输入 CNAME 记录别名，但不含 cdnverify 前缀，然后选择“添加”。

    :::image type="content" source="./media/dns-custom-domain/cdn-add.png" alt-text="CDN 终结点“添加自定义域”页面的屏幕截图。":::

1. 返回 DNS 区域，创建 CNAME 记录，但不含 cdnverify 前缀。  此后，可以安全删除带有 cdnverify 前缀的 CNAME 记录。

    :::image type="content" source="./media/dns-custom-domain/cdn-record-set.png" alt-text="不含 cdnverify 前缀的 CDN 记录的屏幕截图。":::

有关 CDN 以及如何在不执行中间注册步骤的情况下配置自定义域的详细信息，请访问[将 Azure CDN 内容映射到自定义域](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

了解如何[为 Azure 中托管的服务配置反向 DNS](dns-reverse-dns-for-azure-services.md)。
