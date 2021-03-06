---
title: 使用 Azure AD 和 RBAC 访问数据平面
description: 如何使用 Azure Active Directory 基于角色的访问控制访问数据平面。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220106"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>使用 Azure Active Directory 和基于角色的访问控制访问数据平面

本文介绍如何使用 Azure Active Directory (AAD) 基于角色的访问控制 (RBAC) 访问 Azure 春季 Cloud config 服务器和服务注册表终结点。

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>向 AAD 用户/组、MSI 或服务主体分配角色

若要使用 AAD 和 RBAC，必须按以下过程将 *Azure 春季云数据读取器* 角色分配给用户、组或服务主体：

1. 请参阅服务实例的 "服务概述" 页。

2. 单击 " **访问控制 (IAM)** 打开" 访问控制 "边栏选项卡。

3. 单击 " **添加** " 按钮并 **添加角色分配** (可能需要执行授权才能添加) 。

4. 查找并选择 "**角色**" 下的 " *Azure 弹簧云数据读取器*"。
5. `User, group, or service principal`根据用户类型分配对或的访问权限 `User assigned managed identity` 。 搜索并选择 "用户"。  
6. 单击 `Save`

   ![分配角色](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>访问数据平面

为 AAD 用户分配 *Azure 春季 Cloud Data Reader* 角色后，客户可以使用用户、服务主体或托管标识登录到 Azure CLI。  若要获取访问令牌，请参阅 [身份验证 Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) 。  然后，使用以下命令。

```azurecli
az login
az account get-access-token
```

当前，CLI 支持配置服务器和服务注册表的默认终结点。 有关详细信息，请参阅 [生产就绪终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)。 

客户还可以通过访问终结点获取配置服务器和服务注册表的支持终结点的完整列表：
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

标头中的访问令牌提供授权。 仅支持 "GET" 方法。

例如，访问一个终结点（如） *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* 可查看 eureka 的运行状况状态。

根据不同的云类型，下面显示了各种根终结点。

| 云          | 根终结点              |
| -------------- | -------------------------- |
| 公用         | svc.azuremicroservices.io  |
| Mooncake/中国 | svc.microservices.azure.cn |

如果响应为 *401*，则检查是否已成功分配角色。  该角色将需要几分钟时间才能生效，或验证访问令牌是否未过期。

## <a name="next-steps"></a>后续步骤
* [身份验证 Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [生产就绪终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>请参阅
* [创建角色和权限](spring-cloud-howto-permissions.md)