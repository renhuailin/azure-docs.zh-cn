---
title: 为已启用 Azure Arc 的超大规模 PostgreSQL 服务器组配置安全性
description: 为已启用 Azure Arc 的超大规模 PostgreSQL 服务器组配置安全性
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 8841c3abae51de0cfcd1391940f9232c4585c02f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744927"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>为已启用 Azure Arc 的超大规模 PostgreSQL 服务器组配置安全性

本文档介绍与服务器组的安全性相关的各方面：
- 静态加密
- 用户管理
   - 常规透视
   - 更改 _postgres_ 管理用户的密码
- 审核

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>静态加密
可以通过对存储数据库的磁盘进行加密和/或使用数据库函数对插入或更新的数据进行加密，来实现静态加密。

### <a name="hardware-linux-host-volume-encryption"></a>硬件：Linux 主机卷加密
实现系统数据加密来保护在设置启用了 Azure Arc 的数据服务时使用的磁盘上驻留的任何数据。 可以进一步了解此主题：
- Linux 上的常规[静态数据加密](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) 
- 通过 LUKS `cryptsetup` 加密命令 (Linux)(https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) 实现的磁盘加密，具体来说，由于支持 Azure Arc 的数据服务在你提供的物理基础结构上运行，你需要负责保护基础结构。

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>软件：在服务器组中使用 PostgreSQL `pgcrypto` 扩展
除了对用于托管 Azure Arc 设置的磁盘进行加密外，还可以配置已启用 Azure Arc 的超大规模 PostgreSQL 服务器组，以便公开应用程序可用于加密数据库中数据的机制。 `pgcrypto` 扩展是 `contrib` Postgres 扩展的一部分，可在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中使用。 可在[此处](https://www.postgresql.org/docs/current/pgcrypto.html)查看有关 `pgcrypto` 扩展的详细信息。
总而言之，可以使用以下命令启用、创建并使用该扩展：


#### <a name="create-the-pgcrypto-extension"></a>创建 `pgcrypto` 扩展
使用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
CREATE EXTENSION pgcrypto;
```

> 可在[此处](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)查看有关如何连接的详细信息。

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>通过列出可随时在服务器组中使用的扩展来进行验证
可以通过列出可在服务器组中使用的扩展来验证 `pgcrypto` 扩展是否可供使用。
使用所选的客户端工具连接到你的服务器组，然后运行标准 PostgreSQL 查询：
```console
select * from pg_extension;
```
如果使用了上述命令启用并创建了 `pgcrypto`，应会看到它。

#### <a name="use-the-pgcrypto-extension"></a>使用 `pgcrypto` 扩展
现在可以调整应用程序的代码，以便使用 `pgcrypto` 提供的函数：
- 常规哈希函数
- 密码哈希函数
- PGP 加密函数
- 原始加密函数
- 随机数据函数

例如，用于生成哈希值。 运行以下命令：

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

返回以下哈希：

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

或，例如：

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

返回以下哈希：

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

或，例如，要存储加密数据（如密码）：

假设我们的应用程序将机密存储在下表中：

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

在创建用户时我对其密码进行加密：

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

现在，我看到密码已加密：

```console
select * from mysecrets;
```

输出：

- USERid: 1
- USERname: Me
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

当我连接我的应用程序并传递一个密码时，它会在 `mysecrets` 表中查找，如果提供给应用程序的密码与表中存储的某个密码匹配，则返回相应用户的名称。 例如：

- 我传递错误的密码：
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   输出 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 我传递正确的密码：

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   输出：

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

这个小示例展示了可以使用 Postgres `pgcrypto` 扩展在已启用 Azure Arc 的超大规模 PostgreSQL 中加密静态数据（存储加密的数据），且你的应用程序可以使用 `pgcrypto` 提供的函数来操作此加密数据。

## <a name="user-management"></a>用户管理
### <a name="general-perspectives"></a>常规透视
可以使用标准 Postgres 方式来创建用户或角色。 但是，如果这样做，这些项目将仅在协调器角色上可用。 在预览期间，这些用户/角色尚不能够访问在服务器组的协调器节点外部和工作器节点上分布的数据。 原因在于，在预览版中，用户定义不会复制到工作器节点。

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>更改 _postgres_ 管理用户的密码
已启用 Azure Arc 的超大规模 PostgreSQL 附带了标准 Postgres 管理用户 postgres，你将在创建服务器组时为其设置密码。
用于更改其密码的命令的常规格式为：
```azurecli
az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
```

其中 `--admin-password` 是布尔值，与 AZDATA_PASSWORD **会话** 环境变量中是否存在某个值相关。
如果 AZDATA_PASSWORD **会话** 环境变量存在且具有某个值，则运行上述命令会将 postgres 用户的密码设置为此环境变量的值。

如果 AZDATA_PASSWORD **会话** 环境变量存在但不具有值或 AZDATA_PASSWORD **会话** 环境变量不存在，则运行上述命令后将提示用户以交互方式输入密码

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>以交互方式更改 postgres 管理用户的密码

1. 删除 AZDATA_PASSWORD **会话** 环境变量或删除其值
2. 运行以下命令：

   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   例如：
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   系统将提示你输入密码并进行确认：
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   在密码更新时，该命令的输出显示为：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>使用 AZDATA_PASSWORD **会话** 环境变量更改 postgres 管理用户的密码：
1. 将 AZDATA_PASSWORD **会话** 环境变量的值设置为所需密码。
2. 运行命令：
   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   例如：
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   
   在密码更新时，该命令的输出显示为：
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> 若要验证 AZDATA_PASSWORD 会话的环境变量是否存在以及所具有的值，请运行：
> - 在 Linux 客户端上：
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - 在带有 PowerShell 的 Windows 客户端上：
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>审核

如果要用于审核方案，请将服务器组配置为使用 Postgres 的 `pgaudit` 扩展。 有关 `pgaudit` 的更多详细信息，请参阅 [`pgAudit`GitHub 项目](https://github.com/pgaudit/pgaudit/blob/master/README.md)。 若要在服务器组中启用 `pgaudit` 扩展，请阅读[使用 PostgreSQL 扩展](using-extensions-in-postgresql-hyperscale-server-group.md)。



## <a name="next-steps"></a>后续步骤
- 请见[`pgcrypto`扩展](https://www.postgresql.org/docs/current/pgcrypto.html)
- 请阅读[使用 PostgreSQL 扩展](using-extensions-in-postgresql-hyperscale-server-group.md)

