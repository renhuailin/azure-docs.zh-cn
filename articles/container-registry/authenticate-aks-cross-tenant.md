---
title: 从 AKS 群集向另一个 AD 租户中的 Azure 容器注册表进行身份验证
description: 为 AKS 群集的服务主体配置访问另一个 AD 租户中的 Azure 容器注册表的权限
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/13/2021
ms.openlocfilehash: da7a03e1f46523b67666a4a0ab7ab53fba909720
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545465"
---
# <a name="pull-images-from-a-container-registry-to-an-aks-cluster-in-a-different-azure-ad-tenant"></a>将映像从容器注册表拉取到另一个 Azure AD 租户中的 AKS 群集

在某些情况下，Azure AKS 群集可能位于一个 Azure Active Directory (Azure AD) 租户中，而 Azure 容器注册表位于另一个租户中。 本文介绍了使用 AKS 服务主体凭据从容器注册表中进行拉取以启用跨租户身份验证的步骤。

> [!NOTE]
> 如果群集和容器注册表位于不同租户中，则不能附加注册表并使用 AKS 托管标识进行身份验证。

## <a name="scenario-overview"></a>方案概述
本示例假设：

* AKS 群集位于租户 A 中，而 Azure 容器注册表位于租户 B 中 。 
* 在租户 A 中为 AKS 群集配置了服务主体身份验证。详细了解如何[为 AKS 群集创建和使用服务主体](../aks/kubernetes-service-principal.md)。

至少需要 AKS 群集订阅中的“参与者”角色和容器注册表订阅中的“所有者”角色。

可执行下文中的步骤来完成以下操作：

* 在租户 A 中创建一个新的多租户应用（服务主体）。 
* 在租户 B 中预配应用。
* 将服务主体配置为从租户 B 中的注册表中拉取
* 更新租户 A 中的 AKS 群集，以使用新的服务主体进行身份验证


## <a name="step-by-step-instructions"></a>分步说明

### <a name="step-1-create-multitenant-azure-ad-application"></a>步骤 1：创建多租户 Azure AD 应用程序

1. 登录到租户 A 中的 [Azure 门户](https://portal.azure.com/)。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册”>“+ 新建注册” 。
1. 在“支持的帐户类型”中，选择“任何组织目录中的帐户”。
1. 在“重定向 URI”中输入 https://www.microsoft.com。
1. 选择“注册”。
1. 在“概述”页上，记下应用程序（客户端）ID 。 将在步骤 2 和步骤 4 中使用该 ID。

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/service-principal-overview.png" alt-text="服务主体应用程序 ID":::
1. 在“证书和机密”中的“客户端机密”下，选择“+ 新建客户端机密”  。
1. 输入说明（例如“密码”），然后选择“添加”。
1. 在“客户端机密”中，记下客户端机密的值。 在步骤 4 中将使用该值更新 AKS 群集的服务主体。

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/configure-client-secret.png" alt-text="配置客户端机密":::
### <a name="step-2-provision-the-service-principal-in-the-acr-tenant"></a>步骤 2：在 ACR 租户中预配服务主体

1. 使用租户 B 中的管理员帐户打开以下链接。如有指示，请插入租户 B 的 ID 和多租户应用的应用程序 ID（客户端 ID）  。

    ```console
    https://login.microsoftonline.com/<Tenant B ID>/oauth2/authorize?client_id=<Multitenant application ID>&response_type=code&redirect_uri=<redirect url>
    ```
1. 依次选择“代表组织同意”和“接受” 。 
    
    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-consent.png" alt-text="向租户授予应用程序访问权限":::
 

### <a name="step-3-grant-service-principal-permission-to-pull-from-registry"></a>步骤 3：向服务主体授予从注册表拉取的权限

在租户 B中，将 AcrPull 角色分配给服务主体，范围限定为目标容器注册表。 可以使用 [Azure 门户](../role-based-access-control/role-assignments-portal.md)或其他工具来分配角色。 有关使用 Azure CLI 的示例步骤，请参阅[使用服务主体进行 Azure 容器注册表身份验证](container-registry-auth-service-principal.md#use-an-existing-service-principal)。

:::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-acr-pull.png" alt-text="将 acrpull 角色分配给多租户应用":::

### <a name="step-4-update-aks-with-the-azure-ad-application-secret"></a>步骤 4：使用 Azure AD 应用程序机密更新 AKS

使用在步骤 1 中收集的多租户应用程序（客户端）ID 和客户端机密[更新 AKS 服务主体凭据](../aks/update-credentials.md#update-aks-cluster-with-new-service-principal-credentials)。

更新服务主体可能需要几分钟时间。

## <a name="next-steps"></a>后续步骤

* 详细了解[使用服务主体进行 Azure 容器注册表身份验证](container-registry-auth-service-principal.md)
* 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)中详细了解映像拉取机密
- 有关详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)
- 详细了解[使用 Azure 容器注册表从 Kubernetes 群集进行身份验证的方案](authenticate-kubernetes-options.md)


