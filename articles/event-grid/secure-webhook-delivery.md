---
title: 在 Azure 事件网格中使用 Azure AD 进行安全的 WebHook 传递
description: 介绍如何将事件传递到受到 Azure Active Directory 通过 Azure 事件网格进行保护的 HTTPS 终结点
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: ef5bbb33f738a102277870c6227813f6ce8ad257
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616832"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>将事件发布到受 Azure Active Directory 保护的终结点
本文介绍如何使用 Azure Active Directory (Azure AD) 来保护事件订阅和 Webhook 终结点之间的连接 。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

本文使用 Azure 门户进行演示，但也可通过 CLI、PowerShell 或 SDK 来启用此功能。

> [!IMPORTANT]
> 2021 年 3 月 30 日，事件创建或更新中引入了附加访问检查，以解决安全漏洞。 订阅服务器客户端的服务主体需要是所有者或已分配有目标应用程序服务主体中的角色。 请按照下面的新说明重新配置 AAD 应用程序。

## <a name="single-tenant-events-with-azure-ad-and-webhooks"></a>使用 Azure AD 和 Webhook 传递单租户事件

![在 Azure 事件网格中使用 Azure AD 进行安全的 WebHook 传递](./media/secure-webhook-delivery/single-tenant-diagram.png)

根据上图执行以下步骤来配置租户。

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>使用 Azure AD 用户配置事件订阅

1. 为已配置为使用 Microsoft 目录（单租户）的 Webhook 创建一个 Azure AD 应用程序。

2. 在租户中打开 [Azure Shell](https://portal.azure.com/#cloudshell/) 并选择 PowerShell 环境。

3. 修改 $webhookAadTenantId 的值以连接到该租户。

    - 变量：
        - $webhookAadTenantId：Azure 租户 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. 打开[以下脚本](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md)，并使用你的标识符更新 $webhookAppObjectId 和$eventSubscriptionWriterUserPrincipalName 的值，然后继续运行该脚本 。

    - 变量：
        - $webhookAppObjectId：为 Webhook 创建的 Azure AD 应用程序 ID
        - $eventSubscriptionWriterUserPrincipalName：要创建事件订阅的用户的 Azure 用户主体名称

    > [!NOTE]
    > 无需修改 $eventGridAppId 的值，对于此脚本，我们会将 AzureEventGridSecureWebhookSubscriber 设置为 $eventGridRoleName 的值  。 请记住，此脚本必须由 [Azure AD 应用程序管理员角色](../active-directory/roles/permissions-reference.md#all-roles)的成员执行。

5. 在门户中创建事件订阅时，请执行以下步骤：

    1. 选择“Web Hook”作为终结点类型。
    2. 指定终结点 URI。
    
        ![选择终结点类型 webhook](./media/secure-webhook-delivery/select-webhook.png)
    3. 选择“创建事件订阅”页顶部的“其他功能”选项卡 。
    4. 在“其他功能”选项卡上，执行以下步骤：
        1. 选择“使用 AAD 身份验证”，并配置租户 ID 和应用程序 ID：
        2. 从脚本输出中复制 Azure AD 租户 ID，将其输入“AAD 租户 ID”字段中。
        3. 从脚本输出中复制 Azure AD 应用程序 ID，将其输入“AAD 应用程序 ID”字段中。 或者，可以使用 AAD 应用程序 ID URI。 有关应用程序 ID URI 的详细信息，请参阅[本文](../app-service/configure-authentication-provider-aad.md)。
    
            ![保护 Webhook 操作](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>使用 Azure AD 应用程序配置事件订阅

1. 为已配置为使用 Microsoft 目录（单租户）的事件网络订阅写入者创建一个 Azure AD 应用程序。

2. 为前面创建的 Azure AD 应用程序创建机密并保存值（稍后需要用到此值）。

3. 转到事件网格主题中的“访问控制(IAM)”，将事件网格订阅写入者角色分配添加为“事件网格参与者”，通过此步骤，我们可以在使用 Azure CLI 通过 Azure AD 应用程序登录到 Azure 后访问事件网格资源。

4. 为已配置为使用 Microsoft 目录（单租户）的 Webhook 创建一个 Azure AD 应用程序。

5. 在租户中打开 [Azure Shell](https://portal.azure.com/#cloudshell/) 并选择 PowerShell 环境。

6. 修改 $webhookAadTenantId 的值以连接到该租户。

    - 变量：
        - $webhookAadTenantId：Azure 租户 ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. 打开[以下脚本](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md)，并使用你的标识符更新 $webhookAppObjectId 和$eventSubscriptionWriterAppId 的值，然后继续运行该脚本 。

    - 变量：
        - $webhookAppObjectId：为 Webhook 创建的 Azure AD 应用程序 ID
        - $eventSubscriptionWriterAppId：事件网格订阅写入者的 Azure AD 应用程序 ID

    > [!NOTE]
    > 无需修改 ```$eventGridAppId``` 的值，对于此脚本，我们会将 AzureEventGridSecureWebhookSubscriber 设置为 ```$eventGridRoleName``` 的值  。 请记住，此脚本必须由 [Azure AD 应用程序管理员角色](../active-directory/roles/permissions-reference.md#all-roles)的成员执行。

8. 运行以下命令，通过 Azure AD 应用程序以事件网格订阅写入者的身份登录。

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 运行以下命令以创建订阅。

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 在此方案中，我们使用的是事件网格系统主题。 如果你要使用 Azure CLI 创建自定义主题或事件网格域的订阅，请参阅[此文](/cli/azure/eventgrid)。

10. 如果正确配置了所有设置，则可以在事件网格主题中成功创建 Webhook 订阅。

    > [!NOTE]
    > 事件网格此时正在每条消息中向 Webhook 客户端传递 Azure AD 持有者令牌，你需要验证 Webhook 中的授权令牌。

## <a name="multitenant-events-with-azure-ad-and-webhooks"></a>使用 Azure AD 和 Webhook 传递多租户事件

若要跨多个租户启用安全的 Webhook 订阅，需要使用 Azure AD 应用程序执行此任务，目前无法使用 Azure AD 用户身份在门户中完成此过程。

![使用 Azure AD 和 Webhook 传递多租户事件](./media/secure-webhook-delivery/multitenant-diagram.png)

根据上图执行以下步骤来配置两个租户。

1. 在租户 A 中，为已配置为使用任何 Azure AD 目录（多租户）的事件网格订阅写入者创建一个 Azure AD 应用程序。

2. 为前面在租户 A 中创建的 Azure AD 应用程序创建机密并保存值（稍后需要用到此值）。

3. 在租户 A 中，转到事件网格主题中的“访问控制(IAM)”，将事件网格订阅写入者的 Azure AD 应用程序角色分配添加为“事件网格参与者”，通过此步骤，我们可以在使用 Azure CLI 通过 Azure AD 应用程序登录到 Azure 后访问事件网格资源。

4. 在租户 B 中，为已配置为使用 Microsoft 目录（单租户）的 Webhook 创建一个 Azure AD 应用程序。

5. 在租户 B 中打开 [Azure Shell](https://portal.azure.com/#cloudshell/) 并选择 PowerShell 环境。

6. 修改 $webhookAadTenantId 值以连接到租户 B 。

    - 变量：
        - $webhookAadTenantId：租户 B 的 Azure 租户 ID 

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. 打开[以下脚本](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md)，并使用你的标识符更新 $webhookAppObjectId 和$eventSubscriptionWriterAppId 的值，然后继续运行该脚本 。

    - 变量：
        - $webhookAppObjectId：为 Webhook 创建的 Azure AD 应用程序 ID
        - $eventSubscriptionWriterAppId：事件网格订阅写入者的 Azure AD 应用程序 ID

    > [!NOTE]
    > 无需修改 ```$eventGridAppId``` 的值，对于此脚本，我们会将 AzureEventGridSecureWebhookSubscriber 设置为 ```$eventGridRoleName``` 的值  。 请记住，此脚本必须由 [Azure AD 应用程序管理员角色](../active-directory/roles/permissions-reference.md#all-roles)的成员执行。

8. 在租户 A 中打开 [Azure Shell](https://portal.azure.com/#cloudshell/) 并运行以下命令，通过 Azure AD 应用程序以事件网格订阅写入者的身份登录。

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 运行以下命令以创建订阅。

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > 在此方案中，我们使用的是事件网格系统主题。 如果你要使用 Azure CLI 创建自定义主题或事件网格域的订阅，请参阅[此文](/cli/azure/eventgrid)。

10. 如果正确配置了所有设置，则可以在事件网格主题中成功创建 Webhook 订阅。

    > [!NOTE]
    > 事件网格此时正在每条消息中向 Webhook 客户端传递 Azure AD 持有者令牌，你需要验证 Webhook 中的授权令牌。

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。