---
title: 清理资源并删除租户 - Azure Active Directory B2C
description: 描述如何删除 Azure AD B2C 租户的步骤。 了解如何删除所有租户资源，然后删除租户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5935847c4abdf3bc9da60937096a19d44fb2d09d
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107982007"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>清理资源并删除租户

完成 Azure AD B2C 教程后，便可删除用于测试或训练的租户。 若要删除租户，需要先删除所有租户资源。 在本文中，你将：

> [!div class="checklist"]
> * 使用“删除租户”选项来确定清理任务
> * 删除租户资源（用户流、标识提供者、应用程序、用户）
> * 删除租户

## <a name="identify-cleanup-tasks"></a>确定清理任务

1. 使用“全局管理员”或“订阅管理员”角色登录到[Azure 门户](https://portal.azure.com/)。 使用在注册 Azure 时使用的同一个工作或学校帐户，或同一个 Microsoft 帐户。

2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。

3. 选择 **Azure Active Directory** 服务。

4. 在“管理”下，选择“属性” 。

5. 在“Azure 资源的访问管理”下，选择“是”，然后选择“保存”。

6. 从 Azure 门户注销，然后重新登录以刷新你的访问权限。 再次使用顶部菜单中的“目录 + 订阅”筛选器来选择包含 Azure AD B2C 租户的目录。

7. 选择 **Azure Active Directory** 服务。

8. 在“概述”页上，选择“删除租户” 。 “所需操作”列指示需要在删除租户前删除的资源。

   ![删除租户任务](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>删除租户资源

如果已在上一部分打开确认页面，则可以使用“所需操作”列中的链接来打开 Azure 门户页面，而且可以在该页删除这些资源。 或者，可以使用以下步骤从 Azure AD B2C 服务内删除租户资源。

1. 使用“全局管理员”或“订阅管理员”角色登录到[Azure 门户](https://portal.azure.com/)。 使用在注册 Azure 时使用的同一个工作或学校帐户，或同一个 Microsoft 帐户。

2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。

3. 选择 Azure AD B2C 服务。 或者，使用搜索框查找并选择“Azure AD B2C”。

4. 删除除你当前登录时所用的管理员帐户之外的所有用户：在“管理”下，选择“用户”。 在“所有用户”页面上，选择每位用户旁边的复选框（当前登录的管理员帐号除外）。 单击“删除”，并在出现提示时选择“是” 。

   ![删除用户](media/tutorial-delete-tenant/delete-users.png)
   
5. 删除应用注册和 b2c-extensions-app：在“管理”下，选择“应用注册”。 选择“所有应用程序”选项卡。选择一个应用程序，然后选择“删除”"。 针对所有应用程序（包括 b2c-extensions-app 应用程序）重复上述操作。

   ![删除应用程序](media/tutorial-delete-tenant/delete-applications.png)

6. 删除已配置的任何标识提供者：在“管理”下，选择“标识提供者”。 选择已配置的标识提供者，然后选择“删除”。

   ![删除标识提供者](media/tutorial-delete-tenant/identity-providers.png)

8. 删除用户流：在“策略”下，选择“用户流”。 选择每位用户流旁边的省略号 (...)，然后选择“删除”。

   ![删除用户流](media/tutorial-delete-tenant/user-flow.png)

9. 删除策略私钥：在“策略”下，选择“Identity Experience Framework”，然后选择“策略私钥”  。 选择每个策略私钥旁边的省略号 (...)，然后选择“删除”。

10. 删除自定义策略：在“策略”下，依次选择“Identity Experience Framework”和“自定义策略”，然后删除所有策略。

## <a name="delete-the-tenant"></a>删除租户

1. 使用“全局管理员”或“订阅管理员”角色登录到[Azure 门户](https://portal.azure.com/)。 使用在注册 Azure 时使用的同一个工作或学校帐户，或同一个 Microsoft 帐户。

2. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。

3. 选择 **Azure Active Directory** 服务。

4. 如果尚未获取访问管理权限，请执行以下操作：

   * 在“管理”下，选择“属性” 。
   * 在“Azure 资源的访问管理”下，选择“是”，然后选择“保存”。
   * 从 Azure 门户注销后，再重新登录以刷新你的访问权限，然后选择“Azure Active Directory”服务。

5. 在“概述”页上，选择“删除租户” 。

   ![删除租户](media/tutorial-delete-tenant/delete-tenant.png)

6. 按照屏幕上的说明完成该过程。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 删除租户资源
> * 删除租户

接下来，详细了解 Azure AD B2C [用户流和自定义策略](user-flow-overview.md)入门指南。
