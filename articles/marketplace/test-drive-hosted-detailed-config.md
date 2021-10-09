---
title: Azure 市场中的托管体验版的详细配置
description: 介绍适用于商业市场中的托管体验版的配置步骤
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 09/27/2021
ms.openlocfilehash: 320d1b309f49b748e48a7a019c43246e8e5ce7c7
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080288"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>托管体验版的详细配置

本文介绍如何为 Dynamics 365 for Customer Engagement & Power Apps 或 Dynamics 365 for Operations 配置托管体验版。

## <a name="configure-for-dynamics-365-customer-engagement--power-apps"></a>针对 Dynamics 365 Customer Engagement & Power Apps 进行配置

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 如果无法访问上述链接，则需要在[此处](https://appsource.microsoft.com/partners/list-an-app)提交请求以发布应用程序。 查看请求后，你将获得访问权限以开始发布过程。
3. 查找现有的 Dynamics 365 for Customer Engagement & Power Apps 产品/服务，或创建一个新的 Dynamics 365 for Customer Engagement & Power Apps 产品/服务。
4. 在“产品/服务设置”页上，选中“启用体验版”复选框并选择一种“体验版类型”（请参阅下面的项目符号），然后选择“保存草稿”。

    [ ![演示选中“启用体验版”复选框。](./media/test-drive/enable-test-drive-check-box-workspaces.png) ](./media/test-drive/enable-test-drive-check-box-workspaces.png#lightbox)

    - **体验版类型**：选择“Microsoft 托管(Dynamics 365 for Customer Engagement & PowerApps)”。 这表示 Microsoft 将托管和维护用于执行体验版用户预配和取消预配的服务。

5. 遵照[说明](./test-drive-azure-subscription-setup.md)，授予在租户中预配和取消预配体验版用户的 Microsoft AppSource 权限。 此步骤将生成下面所述的“Azure AD 应用 ID”和“Azure AD 应用密钥”值。 
6. 请在“体验版” > “技术配置”页中完成这些字段。

    [![说明“体验版技术配置”页。](media/test-drive/technical-config-details-workspaces.png)](media/test-drive/technical-config-details-workspaces.png#lightbox)

    - **最大并发体验版数** - 可以同时运行活动体验版的并发用户数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，请确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议有 3 到 5 个。
    - **体验版持续时间** - 用户的体验版将处于活动状态的小时数。 超过该时间后，将从租户中取消预配该用户。 建议为 2-24 小时，具体取决于应用的复杂性。 如果用户已耗尽时间并想要再次访问体验版，始终可以请求另一个体验版。
    - **实例 URL** – 体验版用户启动体验版时将发送到的 URL。 通常情况下，这是 Dynamics 365 实例的 URL，应用和示例数据便安装在该实例上。 示例值：`https://testdrive.crm.dynamics.com`。
    - **实例 Web API URL** - Dynamics 365 实例的 Web API URL。 登录到 Microsoft Dynamics 365 实例，并导航到“设置” > “自定义” > “开发人员资源” > “实例 Web API”，检索此值并复制此地址 (URL)   。 示例值：

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="实例 Web API 的示例。":::

    - **角色名称** – 为体验版创建的自定义 Dynamics 365 安全角色的名称，也可以使用现有角色的名称。 新角色应具有为登录到 Customer Engagement 实例所需而添加到角色的最低特权。 请参阅[登录到 Microsoft Dynamics 365 所需的最低特权](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365) 这是在用户使用体验版期间为其分配的角色。 示例值：`testdriverole`。
    
        > [!IMPORTANT]
        > 确保未添加安全组检查。 这样就可以将用户同步到 Customer Engagement 实例。

    - **Azure Active Directory 租户 ID** - Dynamics 365 实例的 Azure 租户 ID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory” > “属性”，然后复制目录 ID 。 示例值：172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租户名称** - Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值：`testdrive.onmicrosoft.com`。
    - **Azure Active Directory 应用程序 ID** – 在步骤 5 中创建的 Azure Active Directory (AD) 应用的 ID。 示例值：`53852862-a2ae-4e43-9461-faa49650a096`。
    - **Azure Active Directory 应用程序客户端密码** - 在步骤 5 中创建的 Azure AD 应用的密码。 示例值：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。

7. 请在“体验版市场列表”页中完成这些字段。

    [ ![说明“市场列表详细信息”页。](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **说明** - 体验版概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，此字段支持 HTML（必需）。
    - **用户手册** - PDF 格式用户手册，可帮助体验版用户了解如何使用你的应用（必需）。
    - **体验版演示视频** - 用于展示应用的视频（可选）。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 如果无法访问上述链接，则需要在[此处](https://appsource.microsoft.com/partners/list-an-app)提交请求以发布应用程序。 查看请求后，你将获得访问权限以开始发布过程。
3. 查找现有的 Dynamics 365 for Customer Engagement & Power Apps 产品/服务，或创建一个新的 Dynamics 365 for Customer Engagement & Power Apps 产品/服务。
4. 选中“启用体验版”复选框并选择一种“体验版类型”（参阅下面的项目符号），然后选择“保存”。

    [![选中“启用体验版”复选框。](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - 体验版类型 - 选择“Microsoft 托管(Dynamics 365 for Customer Engagement & Power Apps)”。 这表示 Microsoft 将托管和维护用于执行体验版用户预配和取消预配的服务。

5. 遵照[说明](./test-drive-azure-subscription-setup.md)，授予在租户中预配和取消预配体验版用户的 Microsoft AppSource 权限。 此步骤将生成下面所述的“Azure AD 应用 ID”和“Azure AD 应用密钥”值。 
6. 请在“体验版技术配置”页中完成这些字段。

    [![“体验版技术配置”页。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大并发体验版数** - 可以同时运行活动体验版的并发用户数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，请确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议有 3 到 5 个。
    - **体验版持续时间** - 用户的体验版将处于活动状态的小时数。 超过该时间后，将从租户中取消预配该用户。 建议为 2-24 小时，具体取决于应用的复杂性。 如果用户已耗尽时间并想要再次访问体验版，始终可以请求另一个体验版。
    - **实例 URL**
        - 客户参与 - 一个 URL，体验版用户启动体验版时会被系统发送到该 URL。 通常情况下，这是 Dynamics 365 实例的 URL，应用和示例数据便安装在该实例上。 示例值：`https://testdrive.crm.dynamics.com`。
        - 画布应用 (Power Apps)
            1. 打开“PowerApps 门户”页并登录。
            2. 选择“应用”，然后选择应用中的省略号。
            4. 选择“详细信息”。
            5. 从“详细信息”选项卡复制“Web 链接”：

                [ ![显示“体验版画布应用”窗口。](./media/test-drive/testdrive-canvas-app.png) ](./media/test-drive/testdrive-canvas-app.png#lightbox)

    - 实例 Web API URL
        - 客户参与 - Dynamics 365 实例的 Web API URL。 检索此值，方法是：登录到 Microsoft Dynamics 365 实例，选择“设置” > “自定义” > “开发人员资源” > “实例 Web API”，然后复制地址 (URL)。 示例值：

            :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="实例 Web API 的示例。":::

        - 画布应用 (Power Apps) - 如果不使用 CE/Dataverse 作为画布应用的后端，请使用 `https://localhost` 作为占位符。

    - **角色名称**
        - 客户参与 - 为体验版创建的自定义 Dynamics 365 安全角色的名称。你也可以使用现有的角色。 新角色应具有为登录到 Customer Engagement 实例所需而添加到角色的最低特权。 请参阅[登录到 Microsoft Dynamics 365 所需的最低特权](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365) 这是在用户使用体验版期间为其分配的角色。 示例值：`testdriverole`。
        - 画布应用 (Power Apps) - 如果不使用 CE/Dataverse 作为后端数据源，请使用“NA”。
    
    > [!IMPORTANT]
    > 确保未添加安全组检查。 这样就可以将用户同步到 Customer Engagement 实例。

    - **Azure Active Directory 租户 ID** - Dynamics 365 实例的 Azure 租户 ID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory” > “属性”，然后复制目录 ID 。 示例值：172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租户名称** - Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值：`testdrive.onmicrosoft.com`。
    - **Azure Active Directory 应用程序 ID** – 在步骤 5 中创建的 Azure Active Directory (AD) 应用的 ID。 示例值：`53852862-a2ae-4e43-9461-faa49650a096`。
    - **Azure Active Directory 应用程序客户端密码** - 在步骤 5 中创建的 Azure AD 应用的密码。 示例值：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。

7. 提供市场列表详细信息。 选择“语言”查看其他必填字段。

    [ ![说明“市场列表详细信息”页。](./media/test-drive/marketplace-listing-details-workspaces.png) ](./media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **说明** - 体验版概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，此字段支持 HTML（必需）。
    - **用户手册** - PDF 格式用户手册，可帮助体验版用户了解如何使用你的应用（必需）。
    - **体验版演示视频** - 用于展示应用的视频（可选）。

---

## <a name="configure-for-dynamics-365-operations"></a>为 Dynamics 365 Operations 配置

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 如果无法访问上述链接，则需要在[此处](https://appsource.microsoft.com/partners/list-an-app)提交请求以发布应用程序。 查看请求后，你将获得访问权限以开始发布过程。
3. 查找现有的 Dynamics 365 for Operations 产品/服务，或创建一个新的。
4. 在“产品/服务设置”页上，选中“启用体验版”复选框并选择一种“体验版类型”（请参阅下面的项目符号），然后选择“保存草稿”。

    [![选中“启用体验版”复选框。](media/test-drive/enable-test-drive-check-box-operations-workspaces.png)](media/test-drive/enable-test-drive-check-box-operations-workspaces.png#lightbox)

    - **体验版类型** – 选择“Dynamics 365 for Operations”选项。 这表示 Microsoft 将托管和维护用于执行体验版用户预配和取消预配的服务。

5. 遵照[说明](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)，授予在租户中预配和取消预配体验版用户的 Microsoft AppSource 权限。 此步骤将生成下面所述的“Azure AD 应用 ID”和“Azure AD 应用密钥”值。 
6. 请在“体验版” >  “技术配置”页中完成这些字段。

    [ ![说明“市场技术配置”页。](./media/test-drive/technical-config-details-operations-workspaces.png) ](./media/test-drive/technical-config-details-operations-workspaces.png#lightbox)

    - **最大并发体验版数** - 可以同时运行活动体验版的并发用户数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，请确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议有 3 到 5 个。
    - **体验版持续时间** - 用户的体验版将处于活动状态的小时数。 超过该时间后，将从租户中取消预配该用户。 建议为 2-24 小时，具体取决于应用的复杂性。 如果用户已耗尽时间并想要再次访问体验版，始终可以请求另一个体验版。
    - **实例 URL** – 体验版用户启动体验版时将发送到的 URL。 通常情况下，这是 Dynamics 365 实例的 URL，应用和示例数据便安装在该实例上。 示例值：`https://testdrive.crm.dynamics.com`。
    - **Azure Active Directory 租户 ID** - Dynamics 365 实例的 Azure 租户 ID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory” > “属性”，然后复制目录 ID 。 示例值：172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租户名称** - Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值：`testdrive.onmicrosoft.com`。
    - **Azure Active Directory 应用程序 ID** – 在步骤 5 中创建的 Azure Active Directory (AD) 应用的 ID。 示例值：`53852862-a2ae-4e43-9461-faa49650a096`。
    - **Azure Active Directory 应用程序客户端密码** - 在步骤 5 中创建的 Azure AD 应用的密码。 示例值：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。
    - **试用法律实体** – 提供用于分配试用用户的法律实体。 你可以在 [创建或修改法律实体](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)中创建新的法律实体。
    - **角色名称** – 为体验版创建的自定义 Dynamics 365 安全角色的 AOT（应用程序对象树）名称。 这是在用户使用体验版期间为其分配的角色。

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="“安全配置”页。":::

7. 请在“体验版市场列表”页中完成这些字段。

    [![市场列表详细信息页。](media/test-drive/marketplace-listing-details-ops-workspaces.png)](media/test-drive/marketplace-listing-details-workspaces.png#lightbox)

    - **说明** - 体验版概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，此字段支持 HTML（必需）。
    - **用户手册** - PDF 格式用户手册，可帮助体验版用户了解如何使用你的应用（必需）。
    - **体验版演示视频** - 用于展示应用的视频（可选）。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

1. 登录[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165507)。
2. 如果无法访问上述链接，则需要在[此处](https://appsource.microsoft.com/partners/list-an-app)提交请求以发布应用程序。 查看请求后，你将获得访问权限以开始发布过程。
3. 查找现有的 Dynamics 365 for Operations 产品/服务，或创建一个新的。
4. 选中“启用体验版”复选框并选择一种“体验版类型”（参阅下面的项目符号），然后选择“保存草稿”。

    [![选中“启用体验版”复选框。](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **体验版类型** – 选择“Dynamics 365 for Operations”选项。 这表示 Microsoft 将托管和维护用于执行体验版用户预配和取消预配的服务。

5. 遵照[说明](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)，授予在租户中预配和取消预配体验版用户的 Microsoft AppSource 权限。 此步骤将生成下面所述的“Azure AD 应用 ID”和“Azure AD 应用密钥”值。 
6. 请在“体验版技术配置”页中完成这些字段。

    [![市场技术配置页。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大并发体验版数** - 可以同时运行活动体验版的并发用户数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，请确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议有 3 到 5 个。
    - **体验版持续时间** - 用户的体验版将处于活动状态的小时数。 超过该时间后，将从租户中取消预配该用户。 建议为 2-24 小时，具体取决于应用的复杂性。 如果用户已耗尽时间并想要再次访问体验版，始终可以请求另一个体验版。
    - **实例 URL** – 体验版用户启动体验版时将发送到的 URL。 通常情况下，这是 Dynamics 365 实例的 URL，应用和示例数据便安装在该实例上。 示例值：`https://testdrive.crm.dynamics.com`。
    - **Azure Active Directory 租户 ID** - Dynamics 365 实例的 Azure 租户 ID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory” > “属性”，然后复制目录 ID 。 示例值：172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **Azure Active Directory 租户名称** - Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值：`testdrive.onmicrosoft.com`。
    - **Azure Active Directory 应用程序 ID** – 在步骤 5 中创建的 Azure Active Directory (AD) 应用的 ID。 示例值：`53852862-a2ae-4e43-9461-faa49650a096`。
    - **Azure Active Directory 应用程序客户端密码** - 在步骤 5 中创建的 Azure AD 应用的密码。 示例值：`IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。
    - **试用法律实体** – 提供用于分配试用用户的法律实体。 你可以在 [创建或修改法律实体](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)中创建新的法律实体。
    - **角色名称** – 为体验版创建的自定义 Dynamics 365 安全角色的 AOT（应用程序对象树）名称。 这是在用户使用体验版期间为其分配的角色。

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="“安全配置”页。":::

7. 提供市场列表详细信息。 选择“语言”查看其他必填字段。

    [![市场列表详细信息页。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **说明** - 体验版概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，可在此字段中输入 HTML 内容。
    - **用户手册** - PDF 格式用户手册，可帮助体验版用户了解如何使用你的应用。
    - **体验版演示视频** - 用于展示应用的视频（可选）。

---

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->
