---
title: 访问 Config Server 和服务注册表
titleSuffix: Azure Spring Cloud
description: 如何使用 Azure Active Directory 基于角色的访问控制访问 Config Server 和服务注册表终结点。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 7c792164f30de2c8c1d2614a54aaf79bebfbd873
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860780"
---
# <a name="access-config-server-and-service-registry"></a>访问 Config Server 和服务注册表

本文介绍如何使用 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 来访问由 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>向 Azure AD 用户/组、MSI 或服务主体分配角色

在 [管理组 | 订阅 | 资源组 | 资源]范围 中向 [用户 | 组 | 服务主体 | 托管标识] 分配“[azure-spring-cloud-data-reader](../role-based-access-control/built-in-roles.md#azure-spring-cloud-data-reader)”角色。

有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

## <a name="access-config-server-and-service-registry-endpoints"></a>访问 Config Server 和服务注册表终结点

分配“Azure Spring Cloud 数据读者”角色后，客户便可访问 Spring Cloud Config Server 和 Spring Cloud 服务注册表终结点。 请执行以下过程：

1. 获取访问令牌。 向 Azure AD 用户分配“Azure Spring Cloud 数据读者”角色后，客户便可通过以下命令，使用用户、服务主体或托管标识登录到 Azure CLI，以获取访问令牌。 有关详细信息，请参阅 [Azure CLI 身份验证](/cli/azure/authenticate-azure-cli)。

    ```azurecli
    az login
    az account get-access-token
    ```

2. 编写终结点。 我们支持由 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表的默认终结点。 有关详细信息，请参阅[生产就绪终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)。 客户还可以通过访问终结点，获取由 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表支持的终结点的完整列表：

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'*

>[!NOTE]
> 如果你使用的是 Azure 中国云，请将 `*.azuremicroservices.io` 替换为 `*.microservices.azure.cn`。[了解详细信息](/azure/china/resources-developer-guide#check-endpoints-in-azure)。

3. 使用访问令牌访问编写的终结点。 将访问令牌放在标头中以提供授权：`--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`。  仅支持“GET”方法。

    例如，访问诸如 https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health 的终结点可查看 eureka 的运行状况。

    如果响应为“401 未授权”，则检查是否已成功分配角色。  角色将需要几分钟时间才能生效，或验证访问令牌是否未过期。

## <a name="next-steps"></a>后续步骤

* [Azure CLI 身份验证](/cli/azure/authenticate-azure-cli)
* [生产就绪终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [创建角色和权限](how-to-permissions.md)
