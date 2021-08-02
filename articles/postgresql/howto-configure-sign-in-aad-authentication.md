---
title: 使用 Azure Active Directory - Azure Database for PostgreSQL - 单一服务器
description: 了解有关如何设置 Azure Active Directory (AAD) 以通过 Azure Database for PostgreSQL - 单一服务器进行身份验证的信息
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/26/2021
ms.openlocfilehash: 03f0ab53b4d2db74a18073808295e12fe5adaaaa
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110494777"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>使用 PostgreSQL 通过 Azure Active Directory 进行身份验证

本文将介绍如何使用 Azure Database for PostgreSQL 配置 Azure Active Directory 访问权限以及如何使用 Azure AD 令牌进行连接的步骤。

## <a name="setting-the-azure-ad-admin-user"></a>设置 Azure AD 管理员用户

只有 Azure AD 管理员用户才能为基于 Azure AD 的身份验证创建/启用用户。 我们建议不要使用 Azure AD 管理员进行常规数据库操作，因为这具有提升的用户权限（如 CREATEDB）。

若要设置 Azure AD 管理员（可以使用用户或组），请遵循以下步骤

1. 在 Azure 门户中，选择要为 Azure AD 启用的 Azure Database for PostgreSQL 实例。
2. 在“设置”下，选择“Active Directory 管理员”：

![设置 Azure AD 管理员][2]

3. 在客户租户中选择一个有效的 Azure AD 用户作为 Azure AD 管理员。

> [!IMPORTANT]
> 设置管理员时，将向具有完全管理员权限的 Azure Database for PostgreSQL 服务器添加新用户。 Azure Database for PostgreSQL 中的 Azure AD 管理员用户将拥有角色 `azure_ad_admin`。
> 每个 PostgreSQL 服务器只能创建一个 Azure AD 管理员，选择另一个管理员将覆盖为服务器配置的现有 Azure AD 管理员。 可以指定 Azure AD 组（而不是单个用户）来拥有多个管理员。 

每个 PostgreSQL 服务器只能创建一个 Azure AD 管理员，选择另一个管理员将覆盖为服务器配置的现有 Azure AD 管理员。 可以指定 Azure AD 组（而不是单个用户）来拥有多个管理员。 请注意，你随后将用该组名称登录，以进行管理。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>使用 Azure AD 连接到 Azure Database for PostgreSQL

以下概要关系图概述了将 Azure AD 身份验证与Azure Database for PostgreSQL 配合使用的工作流：

![身份验证流][1]

我们将 Azure AD 集成设计为可与常见 PostgreSQL 工具（例如 psql）结合使用，这些工具并非 Azure AD 感知工具，并且在连接到 PostgreSQL 时仅支持指定用户名和密码。 我们会将 Azure AD 令牌作为密码传递，如上图所示。

目前已测试以下客户端：

- psql 命令行（利用 PGPASSWORD 变量传递令牌，有关详细信息，请参阅步骤 3）
- Azure Data Studio（使用 PostgreSQL 扩展）
- 其他基于 libpq 的客户端（例如常见的应用程序框架和 ORM）
- PgAdmin（在创建服务器时取消选中“立即连接”。 有关详细信息，请参阅步骤 4）

以下是用户/应用程序使用 Azure AD 进行身份验证所需的步骤：

### <a name="prerequisites"></a>先决条件

可以在 Azure Cloud Shell、Azure VM 或本地计算机上继续操作。 请确保已[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="authenticate-with-azure-ad-as-a-single-user"></a>作为单个用户使用 Azure AD 进行身份验证

### <a name="step-1-login-to-the-users-azure-subscription"></a>步骤 1：登录到用户的 Azure 订阅

首先使用 Azure CLI 工具进行 Azure AD 的身份验证。 对于 Azure Cloud Shell 不需要执行此步骤。

```
az login
```

该命令将启动浏览器窗口，以显示 Azure AD 身份验证页。 这一操作需要提供 Azure AD 的用户 ID 和密码。

### <a name="step-2-retrieve-azure-ad-access-token"></a>步骤 2：检索 Azure AD 访问令牌

调用 Azure CLI 工具，获取步骤 1 中经过 Azure AD 身份验证的用户的访问令牌，以访问 Azure Database for PostgreSQL。

示例（适用于公有云）：

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述资源值必须完全按所示方式指定。 对于其他云，可以使用以下命令查看资源值：

```azurecli-interactive
az cloud show
```

对于 Azure CLI 版本 2.0.71 和更高版本，可以在以下更为方便的版本中为所有云指定命令：

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

身份验证成功后，Azure AD 将返回访问令牌：

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

该令牌是一个 Base 64 字符串，该字符串对有关经过身份验证的用户的所有信息进行编码，并且针对的是 Azure Database for PostgreSQL 服务。


### <a name="step-3-use-token-as-password-for-logging-in-with-client-psql"></a>步骤 3：使用令牌作为密码以使用客户端 psql 登录

在连接时，需要将访问令牌用作 PostgreSQL 用户密码。

使用 `psql` 命令行客户端时，需要通过 `PGPASSWORD` 环境变量传递访问令牌，因为访问令牌超出了 `psql` 可以直接接受的密码长度：

Windows 示例：

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS 示例：

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

现在，你可以像往常一样使用 Azure Database for PostgreSQL 启动连接：

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```
### <a name="step-4-use-token-as-a-password-for-logging-in-with-pgadmin"></a>步骤 4：使用令牌作为密码以使用 PgAdmin 登录

要使用 Azure AD 令牌和 pgAdmin 进行连接，需要执行以下步骤：
1. 创建服务器时取消选中“立即连接”选项。
2. 在“连接”选项卡中输入服务器详细信息并保存。
3. 在浏览器菜单中，单击“连接到 Azure Database for PostgreSQL 服务器”
4. 出现提示时，输入 AD 令牌。


连接时的重要注意事项如下：

* `user@tenant.onmicrosoft.com` 是 Azure AD 用户的名称 
* 由于 Azure AD 用户和组名称区分大小写，因此请确保使用 Azure 用户的准确拼写方式。
* 如果名称包含空格，请在每个空格前使用 `\` 以对空格进行转义。
* 访问令牌的有效期介于 5 分钟到 60 分钟之间。 建议你就在启动 Azure Database for PostgreSQL 登录之前获取访问令牌。

现在可以使用 Azure AD 身份验证向 Azure Database for PostgreSQL 服务器进行身份验证。

## <a name="authenticate-with-azure-ad-as-a-group-member"></a>作为组成员使用 Azure AD 进行身份验证

### <a name="step-1-create-azure-ad-groups-in-azure-database-for-postgresql"></a>步骤 1：在 Azure Database for PostgreSQL 中创建 Azure AD 组

若要启用 Azure AD 组以访问数据库，请使用与用户相同的机制，只不过要指定组名称：

示例：

```
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```
登录时，组成员将使用其个人访问令牌，但使用指定为用户名的组名称进行签名。

### <a name="step-2-login-to-the-users-azure-subscription"></a>步骤 2：登录到用户的 Azure 订阅

通过 Azure CLI 工具使用 Azure AD 进行身份验证。 对于 Azure Cloud Shell 不需要执行此步骤。 用户需要是 Azure AD 组的成员。

```
az login
```

### <a name="step-3-retrieve-azure-ad-access-token"></a>步骤 3：检索 Azure AD 访问令牌

调用 Azure CLI 工具，获取步骤 2 中经过 Azure AD 身份验证的用户的访问令牌，以访问 Azure Database for PostgreSQL。

示例（适用于公有云）：

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上述资源值必须完全按所示方式指定。 对于其他云，可以使用以下命令查看资源值：

```azurecli-interactive
az cloud show
```

对于 Azure CLI 版本 2.0.71 和更高版本，可以在以下更为方便的版本中为所有云指定命令：

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

身份验证成功后，Azure AD 将返回访问令牌：

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

### <a name="step-4-use-token-as-password-for-logging-in-with-psql-or-pgadmin-see-above-steps-for-user-connection"></a>步骤 4：使用令牌作为密码通过 psql 或 PgAdmin 登录（请参阅上述用户连接步骤）

作为组成员进行连接时的重要注意事项：
* groupname@mydb 是尝试以其身份连接的 Azure AD 组的名称
* 在 Azure AD 用户/组名称的后面始终要追加服务器名称（例如 @mydb）
* 请确保使用 Azure AD 组名称的准确拼写方式。
* Azure AD 的用户名和组的名称区分大小写
* 作为组进行连接时，请仅使用组名称（例如 GroupName@mydb），而不要使用组成员的别名。
* 如果名称包含空格，请在每个空格前使用 \ 以对空格进行转义。
* 访问令牌的有效期介于 5 分钟到 60 分钟之间。 建议你就在启动 Azure Database for PostgreSQL 登录之前获取访问令牌。
  
现在可以使用 Azure AD 身份验证向 PostgreSQL 服务器进行身份验证。


## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>在 Azure Database for PostgreSQL 中创建 Azure AD 用户

若要向 Azure Database for PostgreSQL 数据库添加 Azure AD 用户，请在连接后执行以下步骤（请参阅后面有关如何连接的部分）：

1. 首先确保 Azure AD 用户 `<user>@yourtenant.onmicrosoft.com` 是 Azure AD 租户中的有效用户。
2. 以 Azure AD 管理员用户身份登录到 Azure Database for PostgreSQL 实例。
3. 在 Azure Database for PostgreSQL 中创建角色 `<user>@yourtenant.onmicrosoft.com`。
4. 使 `<user>@yourtenant.onmicrosoft.com` 成为角色 azure_ad_user 的成员。 这一定只能向 Azure AD 用户提供。

**示例：**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> 通过 Azure AD 对用户进行身份验证时，不会向用户授予访问 Azure Database for PostgreSQL 数据库中的对象的任何权限。 必须手动向用户授予所需的权限。

## <a name="token-validation"></a>令牌验证

Azure Database for PostgreSQL 中的 Azure AD 身份验证确保 PostgreSQL 服务器中存在用户，并通过验证令牌的内容来检查令牌的有效性。 执行以下令牌验证步骤：

- 令牌是否由 Azure AD 签名，并且未被篡改
- 是否由 Azure AD 为与服务器关联的租户颁发令牌
- 令牌尚未过期
- 令牌用于 Azure Database for PostgreSQL 资源（而不是其他 Azure 资源）

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>将现有 PostgreSQL 用户迁移到基于 Azure AD 的身份验证

可以为现有用户启用 Azure AD 身份验证。 有两种要考虑的事例：

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>事例 1：PostgreSQL 用户名与 Azure AD 用户主体名称匹配

在一种不大可能出现的情况下，如果现有用户已与 Azure AD 用户名相匹配，则可以向其授予 `azure_ad_user` 角色，以便为其启用 Azure AD 身份验证：

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

用户现在将可以使用 Azure AD 凭据登录，而不是使用以前配置的 PostgreSQL 用户密码。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>事例 2：PostgreSQL 用户名与 Azure AD 用户主体名称不同

如果 PostgreSQL 用户在 Azure AD 中不存在或者具有另外的用户名，则可以使用 Azure AD 组作为此 PostgreSQL 用户进行身份验证。 可以创建一个名称与 PostgreSQL 用户匹配的 Azure AD 组，然后向现有的 PostgreSQL 用户授予角色 azure_ad_user，从而将现有 Azure Database for PostgreSQL 用户迁移到 Azure AD 中：

```sql
GRANT azure_ad_user TO "DBReadUser";
```

这假定你已在 Azure AD 中创建了组“DBReadUser”。 属于该组的用户现在能够以此用户身份登录到数据库。

## <a name="next-steps"></a>后续步骤

* 查看[通过 Azure Database for PostgreSQL - 单一服务器进行 Azure Active Directory 身份验证](concepts-aad-authentication.md)的总体概念

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
