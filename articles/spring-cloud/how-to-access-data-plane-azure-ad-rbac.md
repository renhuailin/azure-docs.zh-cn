---
title: 访问 Config Server 和服务注册表
titleSuffix: Azure Spring Cloud
description: 如何使用 Azure Active Directory 基于角色的访问控制访问 Config Server 和服务注册表终结点。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 16433d5b148d7bc441e375591c64af497cd7b8de
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505326"
---
# <a name="access-config-server-and-service-registry"></a>访问 Config Server 和服务注册表

本文介绍如何使用 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 来访问由 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>向 Azure AD 用户/组、MSI 或服务主体分配角色

若要使用 Azure AD 和 RBAC，必须遵循以下过程向用户、组或服务主体分配“Azure Spring Cloud 数据读取者”角色：

1. 转到你的服务实例的服务概述页。

2. 单击“访问控制(IAM)”打开访问控制边栏选项卡。

3. 单击“添加”按钮，然后单击“添加角色分配”（可能需要获得授权才能添加） 。

4. 在“角色”下找到并选择“Azure Spring Cloud 数据读取者”。
5. 根据用户类型分配对 `User, group, or service principal` 或 `User assigned managed identity` 的访问权限。 搜索并选择用户。  
6. 单击 `Save`

   ![assign-role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>访问 Config Server 和服务注册表终结点

分配“Azure Spring Cloud 数据读取者”角色后，客户可以访问 Spring Cloud Config Server 和 Spring Cloud 服务注册表终结点。 请执行以下过程：

1. 获取访问令牌。 为 Azure AD 用户分配“Azure Spring Cloud 数据读取者”角色后，客户可运行以下命令，使用用户身份、服务主体或托管标识登录到 Azure CLI 以获取访问令牌。 有关详细信息，请参阅 [Azure CLI 身份验证](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。 

    ```azurecli
    az login
    az account get-access-token
    ```
2. 组成终结点。 我们支持 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表的默认终结点。 有关详细信息，请参阅[生产就绪的终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)。 客户还可以通过访问终结点，来获取 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表的受支持终结点完整列表：

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

>[!NOTE]
> 如果你使用的是 Azure 中国云，请将 `*.azuremicroservices.io` 替换为 `*.microservices.azure.cn`。[了解详细信息](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)。

3. 使用访问令牌访问组成的终结点。 将访问令牌放在某个头中以提供授权。  仅支持“GET”方法。

    例如，访问诸如 *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* 的终结点可查看 eureka 的运行状况。

    如果响应为“401 未授权”，请检查是否已成功分配角色。  角色需要在几分钟后才能生效。或者，请验证访问令牌是否未过期。

## <a name="next-steps"></a>后续步骤
* [Azure CLI 身份验证](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [生产就绪的终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>请参阅
* [创建角色和权限](how-to-permissions.md)
