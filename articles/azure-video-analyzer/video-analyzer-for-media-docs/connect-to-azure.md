---
title: 创建连接到 Azure 的 Azure 视频分析器媒体版（以前称为视频索引器）帐户
description: 了解如何创建连接到 Azure 的 Azure 视频分析器媒体版（以前称为视频索引器）帐户。
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 13bf5c4eb6fc34848e90b80eb7cd242abf8824c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662129"
---
# <a name="create-a-video-analyzer-for-media-account"></a>创建视频分析器媒体版帐户

创建 Azure 视频分析器媒体版（以前称为视频索引器）帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频分析器媒体版为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以创建连接到 Azure 订阅的视频分析器媒体版帐户。 你需要为索引时间付费，有关详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。

本文介绍如何创建链接到 Azure 订阅和 Azure 媒体服务帐户的视频分析器媒体版帐户。 本主题提供使用自动（默认）流连接 Azure 的步骤。 它还演示如何手动连接到 Azure（高级）。

如果要从“试用”迁移到“付费”视频分析器媒体版帐户，可以如[从试用帐户导入内容](#import-your-content-from-the-trial-account)部分中所述，选择将所有视频和模型自定义复制到新帐户。

本文还介绍了如何[将视频分析器媒体版帐户链接到 Azure 政府云](#video-analyzer-for-media-in-azure-government)。

## <a name="prerequisites-for-connecting-to-azure"></a>连接到 Azure 的先决条件

* Azure 订阅。

    如果没有 Azure 订阅，请注册 [Azure 免费试用](https://azure.microsoft.com/free/)帐户。
* 一个 Azure Active Directory (Azure AD) 域。

    如果没有 Azure AD 域，请使用 Azure 订阅创建此域。 有关详细信息，请参阅[管理 Azure AD 中的自定义域名](../../active-directory/enterprise-users/domains-manage.md)
* Azure AD 域中具有“应用程序管理员”角色的用户。 将视频分析器媒体版帐户连接到 Azure 时，将要使用此成员。

    此用户应是具有工作或学校帐户的 Azure AD 用户。 请勿使用个人帐户，例如 outlook.com、live.com 或 hotmail.com。

    ![所有 Azure AD 用户](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自动流的其他先决条件

* Azure AD 域中的用户和成员。

    将视频分析器媒体版帐户连接到 Azure 时，将要使用此成员。

    此用户应当是 Azure 订阅中具有“所有者”角色，或者兼具“参与者”和“用户访问管理员”两个角色的成员。 可以添加某个用户两次并分配两个角色。 一次分配“参与者”角色，另一次分配“用户访问管理员”角色。 有关详细信息，请参阅[查看用户对 Azure 资源的访问权限](../../role-based-access-control/check-access.md)

    ![访问控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手动流的其他先决条件

* 使用 Azure 门户注册 EventGrid 资源提供程序。

    在 [Azure 门户](https://portal.azure.com/)中，转到“订阅”->[订阅]->“资源提供程序”。

    搜索“Microsoft.Media”和“Microsoft.EventGrid”。 如果该提供程序不是处于“已注册”状态，请单击“注册”。 注册需要花费几分钟时间。

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>在 Azure 中创建新帐户 

> [!NOTE]
> 如果你的 Azure 订阅使用基于证书的多重身份验证，则必须在安装了所需证书的设备上执行以下步骤。

1. 浏览到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 选择“创建无限制帐户”按钮：

    ![创建新的视频分析器媒体版帐户](./media/create-account/create-unlimited-account.png)
1. 订阅列表出现时，请选择要使用的订阅。

    ![将视频分析器媒体版连接到 Azure](./media/create-account/new-account-on-azure-subscription.png)
1. 从支持的位置选择一个 Azure 区域：“美国西部 2”、“北欧”或“东亚”。
1. 在“Azure 媒体服务帐户”下，选择以下选项之一：

    * 若要创建新的媒体服务帐户，请选择“创建新资源组”。 提供资源组的名称。

        Azure 会在订阅中创建新帐户，包括新的 Azure 存储帐户。  
    * 若要使用现有的媒体服务帐户，请选择“使用现有资源”。 从帐户列表中选择自己的帐户。

        媒体服务帐户必须与视频分析器媒体版帐户位于同一区域。

        > [!NOTE]
        > 为了尽量缩短索引持续时间和降低吞吐量，强烈建议在媒体服务帐户中将[预留单位](../../media-services/previous/media-services-scale-media-processing-overview.md )的类型和数量调整为“10 个 S3 预留单位”。 请参阅[使用门户更改预留单位](../../media-services/previous/media-services-portal-scale-media-processing.md)。 按帐户对预留单位进行收费，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/media-services/#analytics)。
    * 若要手动配置连接，请选择“切换到手动配置”链接。

        有关详细信息，请参阅下文[手动连接到 Azure](#connect-to-azure-manually-advanced-option)（高级选项）部分。
1. 完成后，选择“创建”。 此操作可能需要长达数分钟时间才能完成。

    连接到 Azure 后，新的视频分析器媒体版帐户会显示在帐户列表中：

    ![新帐户](./media/create-account/new-account.png)
1. 请确保媒体服务帐户的流式处理终结点正在运行，这样才能在视频分析器媒体版 Web 应用中播放视频（如果视频处于停止状态，请按“开始”）。

> [!TIP]
> 若要为你的帐户指定一个容易记住的显示名称，请转到“设置”。

## <a name="connect-to-azure-manually-advanced-option"></a>手动连接到 Azure（高级选项）

如果与 Azure 的连接失败，你可以尝试通过手动连接来排除并解决问题。

> [!NOTE]
> 强烈建议在同一区域中拥有以下三个帐户：与媒体服务帐户连接的视频分析器媒体版帐户，以及连接到同一媒体服务帐户的 Azure 存储帐户。

### <a name="create-and-configure-a-media-services-account"></a>创建和配置媒体服务帐户

1. 使用 [Azure](https://portal.azure.com/) 门户创建 Azure 媒体服务帐户，如[创建帐户](../../media-services/previous/media-services-portal-create-account.md)中所述。

     请确保已使用经典 API 创建媒体服务帐户。 
 
    ![媒体服务经典 API](./media/create-account/enable-classic-api.png)


    创建媒体服务帐户的存储帐户时，选择“StorageV2”作为帐户类型并在复制字段中选择“异地冗余”(GRS) 。

    ![新建 AMS 帐户](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > 请务必记下媒体服务资源和帐户名。 下一部分的步骤中将用到它们。
1. 在创建的媒体服务帐户中将[预留单位](../../media-services/previous/media-services-scale-media-processing-overview.md )类型和数量调整为“10 个 S3 预留单位”。 请参阅[使用门户更改预留单位](../../media-services/previous/media-services-portal-scale-media-processing.md)。

    按帐户对预留单位进行收费，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/media-services/#analytics)。
1. 必须先启动新媒体服务帐户的默认“流式处理终结点”，才能在视频分析器媒体版 Web 应用中播放视频。

    在新的媒体服务帐户中，选择“流式处理终结点”。 然后选择流式处理终结点并按“启动”。

    ![流式处理终结点](./media/create-account/create-ams-account-se.png)
4. 要让视频分析器媒体版使用媒体服务 API 进行身份验证，需要创建一个 AD 应用。 以下步骤引导你完成[通过 Azure 门户开始使用 Azure AD 身份验证](../../media-services/previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 身份验证过程：

    1. 在新的媒体服务帐户中，选择“API 访问”。
    2. 选择[服务主体身份验证方法](../../media-services/previous/media-services-portal-get-started-with-aad.md)。
    3. 获取客户端 ID 和客户端密码

        在选择“设置”->“密钥”后，添加“说明”并按“保存”，系统会填充密钥值   。

        如果密钥到期，帐户所有者需联系视频分析器媒体版支持人员更新密钥。

        > [!NOTE]
        > 务必记下密钥值和应用程序 ID。 下一部分的步骤中将用到它们。

### <a name="connect-manually"></a>手动连接

在[视频分析器媒体版](https://www.videoindexer.ai/)页的“在 Azure 订阅上新建帐户”对话框中，选择“切换为手动配置”链接 。

在对话框中提供以下信息：

|设置|说明|
|---|---|
|视频分析器媒体版帐户区域|视频分析器媒体版帐户区域的名称。 为了提高性能和降低成本，强烈建议指定 Azure 媒体服务资源和 Azure 存储帐户所在区域的名称。 |
|Azure AD 租户|Azure AD 租户的名称，例如“contoso.onmicrosoft.com”。 可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名上。 找到“域”右侧的名称。|
|订阅 ID|应在其下创建此连接的 Azure 订阅。 可以从 Azure 门户中检索订阅 ID。 选择左侧面板中的“所有服务”，然后搜索“订阅”。 选择“订阅”并从订阅列表中选择所需的 ID。|
|Azure 媒体服务资源组名称|资源组（你在其中创建了媒体服务帐户）的名称。|
|媒体服务资源名称|你在上一节中创建的 Azure 媒体服务帐户的名称。|
|应用程序 ID|你在上一节中创建的 Azure AD 应用程序 ID（具有指定的媒体服务帐户的权限）。|
|应用程序密钥|你在上一节中创建的 Azure AD 应用程序密钥。 |

### <a name="import-your-content-from-the-trial-account"></a>从“试用”帐户导入内容

创建新帐户时，可以选择将“试用”帐户中的内容导入到新帐户。 如果在“在 Azure 订阅上新建帐户”对话框中选中“导入”选项，则会将“试用”帐户中的所有媒体和内容模型自定义复制到新帐户。

内容导入功能对于上述自动和手动方法均有效。

> [!NOTE]
> 只能从每个帐户导入一次内容。
>
> Azure 政府云中不提供“试用”帐户。

## <a name="azure-media-services-considerations"></a>Azure 媒体服务注意事项

请注意以下 Azure 媒体服务相关的事项：

* 如果计划连接到现有的媒体服务帐户，请确保该帐户是通过经典 API 创建的。 
 
    ![媒体服务经典 API](./media/create-account/enable-classic-api.png)
* 如果连接到现有的媒体服务帐户，视频分析器媒体版不会更改现有的媒体“预留单位”配置。

   可能需要根据计划的负载调整媒体“预留单位”的类型和数量。 请记住，如果负载较高，并且没有足够的单位或速度，则视频处理可能导致超时失败。
* 如果连接到新的媒体服务帐户，视频分析器媒体版会自动在其中启动默认的流式处理终结点：

    ![媒体服务流式处理终结点](./media/create-account/ams-streaming-endpoint.png)

    流式处理终结点的启动时间很长。 因此，从将帐户连接到 Azure 到可以在视频分析器媒体版 Web 应用中流式传输和观看视频的这一过程可能需要几分钟时间。
* 如果连接到现有的媒体服务帐户，视频分析器媒体版不会更改默认流式处理终结点配置。 如果未运行任何流式处理终结点，将无法从此媒体服务帐户或者在视频分析器媒体版中观看视频。
* 如果自动连接，视频分析器媒体版会将媒体“预留单位”设置为 10 个 S3 单位：

    ![媒体服务预留单位](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-analyzer-for-media-account"></a>自动创建视频分析器媒体版帐户

若要自动创建帐户，需要执行两个步骤：
 
1. 使用 Azure 资源管理器创建 Azure 媒体服务帐户以及 Azure AD 应用程序。

    请参阅[媒体服务帐户创建模板](https://github.com/Azure-Samples/media-services-v3-arm-templates)示例。
1. 调用[使用媒体服务和 Azure AD 应用程序创建帐户](https://videoindexer.ai.azure.us/account/login?source=apim)。

## <a name="video-analyzer-for-media-in-azure-government"></a>Azure 政府云中的视频分析器媒体版

### <a name="prerequisites-for-connecting-to-azure-government"></a>连接到 Azure 政府的先决条件

-   [Azure 政府](../../azure-government/index.yml)中的 Azure 订阅。
- Azure 政府中的 Azure AD 帐户。
- 权限和资源的所有先决条件（如上[连接到 Azure 的先决条件](#prerequisites-for-connecting-to-azure)中所述）。 请确保检查[自动流的其他先决条件](#additional-prerequisites-for-automatic-flow)和[手动流的其他先决条件](#additional-prerequisites-for-manual-flow)。

### <a name="create-new-account-via-the-azure-government-portal"></a>通过 Azure 政府门户创建新帐户

> [!NOTE]
> Azure 政府云不提供视频分析器媒体版的试用体验。

若要通过视频分析器媒体版门户创建付费帐户，请执行以下操作：

1. 转到 https://videoindexer.ai.azure.us 
1. 使用 Azure 政府 Azure AD 帐户登录。
1.  如果 Azure 政府云中没有你作为所有者或参与者的视频分析器媒体版帐户，则体验内容为空，你可以从中开始创建帐户。 

    接下来的流步骤如上所述，但是只能从可以使用视频分析器媒体版的政府云区域中选择区域 

    如果你已是 Azure 政府云中现有一个或多个视频分析器媒体版帐户的参与者或管理员，你将转到该帐户，并可从其中开始执行以下步骤，以根据需要创建其他帐户（如上文所述）。
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>通过 Azure 政府中的 API 创建新帐户

若要在 Azure 政府中创建付费帐户，请按照[创建付费帐户]()中的说明进行操作。 此 API 终结点仅包括政府云区域。

### <a name="limitations-of-video-analyzer-for-media-on-azure-government"></a>Azure 政府云中视频分析器媒体版的限制

*   政府云中不提供手动内容审核。 

    在公有云中，当内容依据内容审核被视为具有冒犯性时，客户可要求人员查看该内容并可能还原该决策。  
*   无试用帐户。 
* 必应说明 - 在政府云中，我们将不会提供已标识的名人和命名实体的说明。 这只是一个 UI 功能。 

## <a name="clean-up-resources"></a>清理资源

完成本教程后，请删除你不打算使用的资源。

### <a name="delete-a-video-analyzer-for-media-account"></a>删除视频分析器媒体版帐户

如果你要删除视频分析器媒体版帐户，可以从视频分析器媒体版网站删除。 若要删除帐户，你必须是所有者。

选择该帐户 ->“设置” -> “删除此帐户”。 

该帐户将在 90 天内永久删除。

## <a name="firewall"></a>防火墙

请参阅[防火墙后的存储帐户](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall)。

## <a name="next-steps"></a>后续步骤

可以按照[使用 API](video-indexer-use-apis.md) 中的说明，以编程方式来与试用帐户和/或连接到 Azure 的视频分析器媒体版帐户进行交互。

应使用连接到 Azure 时所用的同一 Azure AD 用户。
