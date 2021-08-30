---
title: 为 Azure Spring Cloud 应用程序启用系统分配的托管标识
description: 如何为应用程序启用系统分配的托管标识。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6e600a4634bf2aff23301fe2eb1f9084e181a416
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014820"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识

本文适用于：✔️ Java ✔️ C#

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 资源（如 Azure Spring Cloud 应用程序）提供自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。

本文介绍如何使用 Azure 门户和 CLI（可从 0.2.4 版获得）为 Azure Spring Cloud 应用启用和禁用系统分配的托管标识。

## <a name="prerequisites"></a>先决条件

如果不熟悉 Azure 资源的托管标识，请参阅[概述部分](../active-directory/managed-identities-azure-resources/overview.md)。
需要一个已部署的 Azure Spring Cloud 实例。 按照[使用 Azure CLI 进行部署快速入门](./quickstart.md)进行操作。

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识

若要创建带有系统分配的标识的应用，需在应用程序上设置一个额外的属性。

### <a name="using-azure-portal"></a>使用 Azure 门户

若要在 [Azure 门户](https://portal.azure.com/)中设置托管标识，请首先创建应用，然后启用该功能。

1. 按常规在门户中创建应用。 在门户中导航到该应用。
2. 在左侧导航窗格中向下滚动到“设置”组。
3. 选择“标识”。
4. 在“系统分配的”选项卡中，将“状态”切换为“启用”  。 选择“保存”。

![门户中的托管标识](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

可以在创建应用的过程中或在现有应用上启用系统分配的托管标识。

**在创建应用的过程中启用系统分配的托管标识**

以下示例按 `--assign-identity` 参数的请求，创建名为 app_name 且具有系统分配的托管标识的应用。

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**在现有应用上启用系统分配的托管标识**

使用 `az spring-cloud app identity assign` 命令在现有应用上启用系统分配的标识。

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>获取 Azure 资源的令牌

应用可以使用其托管标识获取令牌，以访问其他受 Azure Active Directory 保护的资源（如 Azure Key Vault）。 这些令牌代表访问资源的应用程序，而不是应用程序的任何特定用户。

可能需要[配置目标资源以允许从应用程序进行访问](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 例如，如果请求用于访问 Key Vault 的令牌，请确保已添加包含应用程序标识的访问策略。 否则，对 Key Vault 的调用将被拒绝，即使其中包含令牌。 若要详细了解支持 Azure Active Directory 令牌的资源，请参阅[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

Azure Spring Cloud 与 Azure 虚拟机使用同一终结点来获取令牌。 建议使用 Java SDK 或 Spring Boot Starter 获取令牌。  有关各种代码和脚本示例以及有关重要主题（例如，处理令牌到期和 HTTP 错误）的指南，请参阅[如何使用 VM 令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

建议：使用 Java SDK 或 Spring Boot Starter 获取令牌。  请参阅[后续步骤](#next-steps)中的示例。

## <a name="disable-system-assigned-identity-from-an-app"></a>从应用禁用系统分配的标识

删除系统分配的标识也会将其从 Azure AD 中删除。 删除应用资源会自动从 Azure AD 中删除系统分配的标识。

### <a name="using-azure-portal"></a>使用 Azure 门户

若要从不再需要系统分配的托管标识的应用中删除该标识，请执行以下操作：

1. 使用与包含 Azure Spring Cloud 实例的 Azure 订阅关联的帐户登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到所需的虚拟机，然后选择“标识”。
1. 在“系统分配”/“状态”下，选择“关闭”，然后选择“保存”：

![托管标识](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI

若要从不再需要系统分配的托管标识的应用中删除该标识，请使用以下命令：

```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>后续步骤

* [在 Spring Boot Starter 中使用托管标识访问 Azure Key Vault](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [如何在 Java SDK 中使用托管标识](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
