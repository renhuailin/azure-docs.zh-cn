---
title: Azure Marketplace 中的托管测试驱动器的详细配置
description: 说明适用于商业应用商店中的托管测试驱动器的配置步骤
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462947"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>托管测试驱动器的详细配置

本文介绍如何为 Dynamics 365 配置用于操作的客户参与或 Dynamics 365 的托管测试驱动器。

## <a name="configure-for-dynamics-365-customer-engagement"></a>为 Dynamics 365 客户参与配置

1. 登录[合作伙伴中心](https://partner.microsoft.com/)。
2. 如果无法访问上述链接，则需要在 [此处](https://appsource.microsoft.com/partners/list-an-app) 提交请求以发布应用程序。 查看请求后，你将获得开始发布过程的访问权限。
3. 查找 **适用于客户参与** 产品/服务的现有 dynamics 365，或创建新 **的 dynamics 365 来提供客户参与** 情况。
4. 选中 " **启用测试驱动器** " 复选框并选择一 **种类型的测试驱动器** (参阅下面的项目符号) ，然后选择 " **保存**"。

    [![选中 "启用测试驱动器" 复选框。](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **测试驱动器的类型** -选择 " **Microsoft 托管 (Dynamics 365 for Customer Engagement & PowerApps)**"。 这表明，Microsoft 将托管和维护执行测试驱动器用户预配和取消预配的服务。

5. 使用 [这些说明](./test-drive-azure-subscription-setup.md)向租户中的用户授予预配和取消预配的 Microsoft AppSource 权限。 在此步骤中，将生成如下所述的 **AZURE AD 应用 ID** 和 **Azure AD 应用密钥** 值。
6. 请在 " **测试驱动器技术配置** " 页上完成这些字段。

    [!["测试驱动器技术配置" 页。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大并发测试驱动器** 数–可以同时运行活动测试驱动器的并发用户数。 每个用户都将在其测试驱动器处于活动状态时使用 Dynamics license，因此请确保至少有可用于测试驱动器用户的众多 Dynamics 许可证。 建议使用3到5。
    - "**测试驱动器持续时间**" –用户的测试驱动器将处于活动状态的小时数。 该时间到期后，将从你的租户取消预配用户。 建议使用2-24 小时，具体取决于应用的复杂性。 如果用户的运行时间不足，并且想要再次访问该测试驱动器，则用户始终可以请求另一个。
    - **实例 url** –测试驱动器用户启动测试驱动器时将发送到的 url。 这通常是在其上安装应用和示例数据的 Dynamics 365 实例的 URL。 示例值： `https://testdrive.crm.dynamics.com` 。
    - **实例 WEB API url** -Dynamics 365 实例的 WEB api url。 通过登录到你的 Microsoft Dynamics 365 实例，导航到 **设置**  >  **自定义**  >  **开发人员资源**  >  **实例 Web API** 并将地址复制 (URL) 来检索此值。 示例值：

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="实例 Web API 的示例。":::

    - **角色名称** –为测试驱动器创建的自定义 Dynamics 365 安全角色的名称，或者可以使用现有角色。 新角色应具有为登录到 Customer Engagement 实例而添加到角色的最低要求的权限。 请参阅 [登录到 Microsoft Dynamics 365 所需的最小特权](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)。 这是在其测试驱动器中将分配给用户的角色。 示例值： `testdriverole` 。
    
        > [!IMPORTANT]
        > 确保未添加安全组检查。 这样，用户就可以同步到客户参与实例。

    - **Azure Active Directory 租户 id** –适用于 Dynamics 365 实例的 Azure 租户的 ID。 若要检索此值，请登录到 Azure 门户，导航到 **Azure Active Directory**  >  **属性** 并复制目录 ID。 示例值： 172f988bf-86f1-41af-722278f-fef1-499f-91ab-2323d011db47-2d7cd01112341。
    - **Azure Active Directory 租户名称** –适用于 Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值： `testdrive.onmicrosoft.com` 。
    - **Azure Active Directory 应用程序 ID** –在步骤5中创建的 AZURE ACTIVE DIRECTORY (AD) 应用的 ID。 示例值： `53852862-a2ae-4e43-9461-faa49650a096` 。
    - Azure Active Directory 在步骤5中创建的 Azure AD 应用的 **应用程序客户端机密**。 示例值： `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 。

7. 提供 marketplace 列表详细信息。 选择 " **语言** " 查看其他必填字段。

    [![Marketplace 列表详细信息页。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **说明** -测试驱动器的概述。 预配测试驱动器时，此文本将显示给用户。 若要提供带格式的内容，可在此字段中输入 HTML 内容。
    - **用户手册** -一种 PDF 用户手册，可帮助测试驱动器用户了解如何使用你的应用程序。
    - 试用 **演示视频**- (可选) 展示应用的视频。

## <a name="configure-for-dynamics-365-operations"></a>配置 Dynamics 365 操作

2. 如果无法访问上述链接，则需要在 [此处](https://appsource.microsoft.com/partners/list-an-app) 提交请求以发布应用程序。 查看请求后，你将获得开始发布过程的访问权限。
3. 查找 **适用于操作的现有 dynamics 365，** 或创建新 **的 Dynamics 365 for operations** 产品/服务。
4. 选中 " **启用测试驱动器** " 复选框并选择一 **种类型的测试驱动器** (参阅下面的项目符号) ，然后选择 " **保存**"。

    [![选中 "启用测试驱动器" 复选框。](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **测试驱动器的类型** – **为操作选择 "Dynamics 365** " 选项。 这意味着，Microsoft 将托管和维护执行测试驱动器用户预配和取消预配的服务。

5. 使用 [这些说明](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)向租户中的用户授予预配和取消预配的 Microsoft AppSource 权限。 在此步骤中，将生成如下所述的 **AZURE AD 应用 ID** 和 **Azure AD 应用密钥** 值。
6. 请在 " **测试驱动器技术配置** " 页上完成这些字段。

    [![Marketplace 技术配置页面。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **最大并发测试驱动器** 数–可以同时运行活动测试驱动器的并发用户数。 每个用户都将在其测试驱动器处于活动状态时使用 Dynamics license，因此请确保至少有可用于测试驱动器用户的众多 Dynamics 许可证。 建议使用3到5。
    - "**测试驱动器持续时间**" –用户的测试驱动器将处于活动状态的小时数。 该时间到期后，将从你的租户取消预配用户。 建议使用2-24 小时，具体取决于应用的复杂性。 如果用户的运行时间不足，并且想要再次访问该测试驱动器，则用户始终可以请求另一个。
    - **实例 url** –测试驱动器用户启动测试驱动器时将发送到的 url。 这通常是在其上安装应用和示例数据的 Dynamics 365 实例的 URL。 示例值： `https://testdrive.crm.dynamics.com` 。
    - **Azure Active Directory 租户 id** –适用于 Dynamics 365 实例的 Azure 租户的 ID。 若要检索此值，请登录到 Azure 门户，导航到 **Azure Active Directory**  >  **属性** 并复制目录 ID。 示例值： 172f988bf-86f1-41af-722278f-fef1-499f-91ab-2323d011db47-2d7cd01112341。
    - **Azure Active Directory 租户名称** –适用于 Dynamics 365 实例的 Azure 租户的名称。 使用格式 `<tenantname>.onmicrosoft.com`。 示例值： `testdrive.onmicrosoft.com` 。
    - **Azure Active Directory 应用程序 ID** –在步骤5中创建的 AZURE ACTIVE DIRECTORY (AD) 应用的 ID。 示例值： `53852862-a2ae-4e43-9461-faa49650a096` 。
    - Azure Active Directory 在步骤5中创建的 Azure AD 应用的 **应用程序客户端机密**。 示例值： `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` 。
    - **试用法律实体** –提供用于分配试用用户的法律实体。 您可以在 [创建或修改法律实体](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)时创建一个新的。
    - **角色名称** –为测试驱动器创建的自定义 Dynamics 365 安全角色的 AOT 名称 (应用程序对象树) 。 这是在其测试驱动器中将分配给用户的角色。

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="&quot;安全配置&quot; 页。":::

7. 提供 marketplace 列表详细信息。 选择 " **语言** " 查看其他必填字段。

    [![Marketplace 列表详细信息页。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **说明** -测试驱动器的概述。 预配测试驱动器时，此文本将显示给用户。 若要提供带格式的内容，可在此字段中输入 HTML 内容。
    - **用户手册** -一种 PDF 用户手册，可帮助测试驱动器用户了解如何使用你的应用程序。
    - 试用 **演示视频**- (可选) 展示应用的视频。

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->