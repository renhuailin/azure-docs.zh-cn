---
title: Azure Data Lake Storage Gen2 中的访问控制列表
description: 了解 Azure Data Lake Storage Gen2 中的 POSIX 样式的 ACL 访问控制列表如何工作。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: aee02d46f0932a084dccb4b1bc4c8f288a372104
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552267"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)

Azure Data Lake Storage Gen2 实现了一个访问控制模型，该模型支持 Azure 基于角色的访问控制 (Azure RBAC) 和类似 POSIX 的访问控制列表 (ACL)。 本文介绍了 Data Lake Storage Gen2 中的访问控制列表。 若要了解如何结合使用 Azure RBAC 和 ACL，以及系统如何评估它们以做出授权决策，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)。

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>关于 ACL

可将[安全主体](../../role-based-access-control/overview.md#security-principal)关联到对文件和目录的访问权限级别。 每个关联都捕获为访问控制列表 (ACL) 中的一个条目。 存储帐户中的每个文件和目录都有一个访问控制列表。 当安全主体尝试对文件或目录执行操作时，ACL 检查将确定安全主体（用户、组、服务主体或托管标识）是否具有执行该操作所需的正确权限级别。

> [!NOTE]
> ACL 仅应用于同一个租户中的安全主体，不应用于使用共享密钥或共享访问签名 (SAS) 令牌身份验证的用户。 那是因为没有与调用方关联的标识，因此不能执行基于安全主体权限的授权。

<a id="set-access-control-lists"></a>

## <a name="how-to-set-acls"></a>如何设置 ACL

若要设置文件和目录级权限，请参阅以下任一文章：

| 环境 | 项目 |
|--------|-----------|
|Azure 存储资源管理器 |[使用 Azure 存储资源管理器在 Azure Data Lake Storage Gen2 中管理 ACL](data-lake-storage-explorer-acl.md)|
|Azure 门户 |[使用 Azure 门户管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-azure-portal.md)|
|.NET |[使用 .NET 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-dotnet.md)|
|Java|[使用 Java 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-java.md)|
|Python|[使用 Python 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-python.md)|
|JavaScript (Node.js)|[使用 Node.js 中的 JavaScript SDK 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-directory-file-acl-javascript.md)|
|PowerShell|[使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-powershell.md)|
|Azure CLI|[使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-cli.md)|
|REST API |[路径 - 更新](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> 如果安全主体是服务主体，则必须使用该服务主体的对象 ID，而不能使用相关应用注册的对象 ID。 若要获取服务主体的对象 ID，请打开 Azure CLI，然后使用此命令：`az ad sp show --id <Your App ID> --query objectId`。 请务必将 `<Your App ID>` 占位符替换为应用注册的应用 ID。

## <a name="types-of-acls"></a>ACL 的类型

访问控制列表有两种类型：访问 ACL 和默认 ACL。 

访问 ACL 控制对某个对象的访问权限。 文件和目录都具有访问 ACL。

默认 ACL 是与目录关联的 ACL 模板，用于确定在该目录下创建的任何子项的访问 ACL。 文件没有默认 ACL。

访问 ACL 和默认 ACL 具有相同的结构。

> [!NOTE]
> 更改父级的默认 ACL 不影响现有子项的访问 ACL 或默认 ACL。

## <a name="levels-of-permission"></a>权限级别

容器中目录和文件的权限为“读取”、“写入”和“执行”，可对文件和目录使用这些权限，如下表所示：  

|            |    文件     |   Directory |
|------------|-------------|----------|
| **读取 (R)** | 可以读取文件内容 | 需有“读取”和“执行”权限才能列出目录内容  |
| **写入 (W)** | 可以在文件中写入或追加内容 | 需有“写入”和“执行”权限才能在目录中创建子项  |
| **执行 (X)** | 不表示 Data Lake Storage Gen2 上下文中的任何内容 | 需要遍历目录的子项 |

> [!NOTE]
> 如果仅使用 ACL（不使用 Azure RBAC）授予权限，则要授予安全主体对文件的读取或写入访问权限，需要授予安全主体对容器根文件夹以及通向该文件的文件夹层次结构中每个文件夹的“执行”权限。

### <a name="short-forms-for-permissions"></a>权限的简短形式

**RWX** 用于表示“读取 + 写入 + 执行”。 还有更精简的数字形式，“读取=4”，“写入=2”，“执行=1”，其总和表示各种不同的权限。   下面是一些示例。

| 数字形式 | 简短形式 |      含义     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 读取 + 写入 + 执行 |
| 5            | `R-X`        | 读取 + 执行         |
| 4            | `R--`        | 读取                   |
| 0            | `---`        | 无权限         |

### <a name="permissions-inheritance"></a>权限继承

在 Data Lake Storage Gen2 使用的 POSIX 样式的模型中，项的权限存储在项本身中。 换而言之，如果是在已创建子项后设置的权限，则不能从父项继承项的权限。 只有于创建子项前在父项上设置了默认权限时，才能继承权限。

## <a name="common-scenarios-related-to-acl-permissions"></a>与 ACL 权限相关的常见场景

下表显示了让安全主体执行“操作”列中列出的操作所需的 ACL 条目。

此表显示的列代表虚构目录层次结构的每个级别。 容器的根目录 (`/`)、名为 **Oregon** 的子目录、Oregon 目录的名为 **Portland** 的子目录以及 Portland 目录中名为 **Data.txt** 的文本文件分别对应一个列。

> [!IMPORTANT]
> 此表假设你仅在使用 ACL，没有使用任何 Azure 角色分配。 若要查看组合使用 Azure RBAC 和 ACL 的类似表，请参阅[权限表：组合使用 Azure RBAC 和 ACL](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)。

|    操作             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> 只要以上两个条件成立，删除文件时就不需要文件的写入权限。

## <a name="users-and-identities"></a>用户和标识

每个文件和目录都有这些标识的不同权限：

- 拥有用户
- 拥有组
- 命名用户
- 命名组
- 命名服务主体
- 命名托管标识
- 所有其他用户

用户和组的标识是 Azure Active Directory (Azure AD) 标识。 因此，除非另有规定，否则“用户”在 Data Lake Storage Gen2 的上下文中可以表示 Azure AD 用户、服务主体、托管标识或安全组。

### <a name="the-owning-user"></a>拥有用户

创建项的用户自动成为该项的拥有用户。 拥有用户可以：

- 更改所拥有文件的权限。
- 更改所拥有文件的拥有组，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 拥有用户无法更改某个文件或目录的拥有用户。 只有超级用户可以更改文件或目录的拥有用户。

### <a name="the-owning-group"></a>拥有组

在 POSIX ACL 中，每个用户都与“主组”关联。 例如，用户“Alice”可能属于“finance”组。 Alice 还可能属于多个组，但始终有一个组指定为她的主组。 在 POSIX 中，当 Alice 创建文件时，该文件的拥有组设置为她的主组，在本例中为“finance”。 否则，所有者组的行为类似于为其他用户/组分配的权限。

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>为新的文件或目录分配拥有组

- 案例 1：根文件夹 `/`。 此目录是在创建 Data Lake Storage Gen2 容器时创建的。 在这种情况下，如果容器是使用 OAuth 创建的，则拥有组将设置为创建容器的用户。 如果容器是使用共享密钥、帐户 SAS 或服务 SAS 创建的，则所有者和拥有组将设置为 `$superuser`。
- 案例 2（所有其他情况）：创建新项时，从父目录复制拥有组。

#### <a name="changing-the-owning-group"></a>更改拥有组

拥有组可由以下用户更改：

- 任何超级用户。
- 拥有用户，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 拥有组无法更改某个文件或目录的 ACL。 虽然拥有组设置为在根目录那一种情况（即上面的 **案例 1**）中创建了帐户的用户，但单个用户帐户不能有效地用于通过拥有组提供权限。 可以将此权限分配给有效的用户组（如果适用）。

## <a name="how-permissions-are-evaluated"></a>权限是如何评估的

标识按以下顺序评估：

1. 超级用户
2. 拥有用户
3. 已命名用户、服务主体或托管标识
4. 拥有组或已命名组
5. 所有其他用户

如果这些标识中的多个标识应用于安全主体，则授予与第一个标识关联的权限级别。 例如，如果安全主体既是拥有用户也是命名用户，则应用与拥有用户关联的权限级别。

以下伪代码显示了存储帐户的访问检查算法。 此算法显示标识的评估顺序。

```python
def access_check( user, desired_perms, path ) :
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm

  # Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask isn't used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True

  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>掩码

如访问检查算法中所示，掩码会限制对命名用户、拥有组和命名组的访问权限。

对于新的 Data Lake Storage Gen2 容器，根目录（“/”）的访问 ACL 的掩码默认为 **750**（适用于目录）和 **640**（适用于文件）。 下表显示了这些权限级别的符号表示法。

|实体|目录|文件|
|--|--|--|
|拥有用户|`rwx`|`rw-`|
|拥有组|`r-x`|`r--`|
|其他|`---`|`---`|

文件不接收 X 位，因为它与只存储系统中的文件无关。

可能会在每次调用时指定掩码。 这就使不同的使用系统（例如群集）能够为文件操作使用不同的有效掩码。 如果根据特定请求指定了掩码，则该掩码完全替代默认掩码。

### <a name="the-sticky-bit"></a>粘滞位

粘滞位是 POSIX 容器的更高级功能。 在 Data Lake Storage Gen2 的上下文中，不太可能需要粘滞位。 总之，如果目录上已启用粘滞位，子项只能由子项的拥有用户删除或重命名。

粘滞位不会显示在 Azure 门户中。

## <a name="default-permissions-on-new-files-and-directories"></a>新文件和目录的默认权限

在现有目录下创建新文件或目录时，父目录的默认 ACL 会确定：

- 子目录的默认 ACL 和访问 ACL。
- 子文件的访问 ACL（文件没有默认 ACL）。

### <a name="umask"></a>umask

创建文件或目录时，umask 用于修改默认 ACL 在子项上的设置方式。 umask 是父目录上一个 9 位的值，它包含“拥有用户”、“拥有组”和“其他”的 RWX 值  。

Azure Data Lake Storage Gen2 的 umask 是设置为 007 的常量值。 此值将转换为：

| umask 组件     | 数字形式 | 简短形式 | 含义 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 对于拥有用户，将父项的默认 ACL 复制到子项的访问 ACL |
| umask.owning_group  |    0         |   `---`      | 对于拥有组，将父项的默认 ACL 复制到子项的访问 ACL |
| umask.other         |    7         |   `RWX`      | 对于其他，删除子项的访问 ACL 的所有权限 |

Azure Data Lake Storage Gen2 使用的 umask 值实际上表示默认情况下永远不会在新子项上传输“other”的值，除非在父目录上定义了默认的 ACL。 在这种情况下，umask 实际上会被忽略，默认 ACL 定义的权限将应用于子项。

以下伪代码显示了在为子项创建 ACL 时如何应用 umask。

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="faq"></a>常见问题解答

### <a name="do-i-have-to-enable-support-for-acls"></a>是否必须启用 ACL 的支持？

否。 只要开启了分层命名空间 (HNS) 功能，存储帐户就能通过 ACL 进行访问控制。

如果关闭了 HNS 功能，Azure RBAC 授权规则仍适用。

### <a name="what-is-the-best-way-to-apply-acls"></a>应用 ACL 的最佳方式是什么？

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>如何评估 Azure RBAC 和 ACL 权限？

若要了解系统如何将 Azure RBAC 和 ACL 一起评估，以便针对存储帐户资源做出授权决策，请参阅[如何评估权限](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)。

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Azure 角色限制和 ACL 条目存在哪些限制？

下表提供了在使用 Azure RBAC 管理“粗粒度”权限（应用于存储帐户或容器的权限）以及使用 ACL 管理“细粒度”权限（应用于文件和目录的权限）时要考虑的限制的汇总视图。 使用安全组进行 ACL 分配。 通过使用组，你不太可能超出每个订阅的角色分配的最大数量，以及每个文件或目录的 ACL 条目的最大数量。

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)]

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2 是否支持 Azure RBAC 继承？

Azure 角色分配确实可以继承。 分配从订阅、资源组和存储帐户资源向下传递到容器资源。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 是否支持 ACL 继承？

可以使用默认 ACL 来设置父目录下创建的新子目录和文件的 ACL。 若要更新现有子项的 ACL，你需要以递归方式为所需的目录层次结构添加、更新或删除 ACL。 有关指南，请参阅本文的[如何设置 ACL](#set-access-control-lists) 部分。

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>以递归方式删除目录及其内容需要哪些权限？

- 调用方需要拥有“超级用户”权限，

或

- 父目录必须拥有“写入 + 执行”权限。
- 要删除的目录及其中的每个目录都需要“读取 + 写入 + 执行”权限。

> [!NOTE]
> 删除目录中的文件不需要具有“写入”权限。 另外，永远无法删除根目录“/”。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>谁是文件或目录的所有者？

文件或目录的创建者就是所有者。 就根目录而言，这就是创建容器的用户的标识。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>创建文件或目录时将哪个组设置为其拥有组？

拥有组是从创建新文件或目录的父目录的拥有组复制而来的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>我是文件的拥有用户，但没有所需的 RWX 权限， 我该怎么办？

拥有用户只需更改文件的权限，即可自动获得所需的任何 RWX 权限。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>为什么我时候在 ACL 中看到了 GUID？

如果条目表示一个用户且该用户不再位于 Azure AD 中，则显示 GUID。 当用户离职，或者其帐户已在 Azure AD 中删除时，往往会发生这种情况。 此外，服务主体和安全组没有用于标识它们的用户主体名称 (UPN)，因此用它们的 OID 属性（即一个 GUID）来表示它们。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>如何为服务主体正确设置 ACL？

为服务主体定义 ACL 时，必须使用所创建应用注册的服务主体的对象 ID (OID)。 请务必注意，注册的应用在特定的 Azure AD 租户中具有独立的服务主体。 注册的应用会在 Azure 门户中显示一个 OID，但服务主体具有另一个（不同的）OID。

若要获取服务主体的对应于应用注册的 OID，可以使用 `az ad sp show` 命令。 指定应用程序 ID 作为参数。 以下示例获取服务主体的 OID，该 OID 对应于应用 ID 为 18218b12-1895-43e9-ad80-6e8fc1ea88ce 的应用注册。 在 Azure CLI 中运行以下命令：

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

随即显示 OID。

获取服务主体的正确 OID 后，转到存储资源管理器的“管理访问权限”页，以添加该 OID 并为其分配适当的的权限。 请务必选择“保存”。

### <a name="can-i-set-the-acl-of-a-container"></a>是否可以设置容器的 ACL？

错误。 容器没有 ACL。 但是，你可以设置容器的根目录的 ACL。 每个容器都有一个根目录，该目录与容器同名。 例如，如果容器名为 `my-container`，则根目录名为 `my-container/`。

Azure 存储 REST API 包含一个名为[设置容器 ACL](/rest/api/storageservices/set-container-acl) 的操作，但该操作不能用来设置容器或容器根目录的 ACL， 而只能用来指示容器中的 blob [是否可以公开访问](anonymous-read-access-configure.md)。

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>在哪里可以了解 POSIX 访问控制模型的详细信息？

- [Linux 上的 POSIX 访问控制列表](https://www.linux.com/news/posix-acls-linux)
- [HDFS 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
- [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
- [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
- [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
- [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
- [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
- [在 Linux 上使用访问控制列表 (ACL)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control-model.md)
