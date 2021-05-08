---
title: 了解 IoT 中心设备更新身份验证和授权 | Microsoft Docs
description: 了解 IoT 中心设备更新如何使用 Azure RBAC 为用户和服务 API 提供身份验证和授权。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101661842"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure 基于角色的访问控制 (RBAC) 和设备更新

设备更新使用 Azure RBAC 为用户和服务 API 提供身份验证和授权。

## <a name="configure-access-control-roles"></a>配置访问控制角色

为了使其他用户和应用程序有权访问设备更新，必须为用户和应用程序授予对此资源的访问权限。 下面是设备更新支持的角色

|   角色名称   | 说明  |
| :--------- | :---- |
|  设备更新管理员 | 有权访问所有设备更新资源  |
|  设备更新读取者| 可以查看所有更新和部署 |
|  设备更新内容管理员 | 可以查看、导入和删除更新  |
|  设备更新内容读取者 | 可以查看更新  |
|  设备更新部署管理员 | 可以管理设备更新部署|
|  设备更新部署读取者| 可以查看设备更新部署 |

可以使用组合角色提供正确的访问级别。 例如，开发人员可以使用设备更新内容管理员角色导入和管理更新，但需要设备更新部署读取者角色才能查看更新进度。 相反，具有设备更新读取者角色的解决方案操作员可以查看所有更新，但需要使用设备更新部署管理员角色才能将特定更新部署到设备。


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>对设备更新 REST API 进行身份验证以供发布和管理

设备更新还使用 Azure AD 进行身份验证，以通过服务 API 发布和管理内容。 若要开始使用，你需要创建并配置客户端应用程序。

### <a name="create-client-azure-ad-app"></a>创建客户端 Azure AD 应用

若要将某个应用程序或服务与 Azure AD 进行集成，请先将该应用程序[注册](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)到 Azure AD。 根据所使用的授权流，客户端应用程序的安装会有所不同。  下面是使用设备更新 REST API 时的配置指导。

* 设置客户端身份验证：“用于本机或 Web 客户端的重定向 URI”。
* 设置 API 权限 - IoT 中心设备更新公开：
  * 委托的权限：“user_impersonation”
  * 可选，授予管理员同意

[后续步骤：创建设备更新资源并配置访问控制角色](./create-device-update-account.md)
