---
title: Data Lake Storage Gen1 中的访问控制概述 | Microsoft Docs
description: 了解派生自 HDFS 的 Azure Data Lake Storage Gen1 的访问控制模型的基础知识。
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 76e04c749af8e2152bb7fd1ab18a4bc5589b259f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680521"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 中的访问控制

Azure Data Lake Storage Gen1 实现派生自 HDFS 的访问控制模型，而 HDFS 又派生自 POSIX 访问控制模型。 本文汇总了 Data Lake Storage Gen1 访问控制模型的基本知识。 

## <a name="access-control-lists-on-files-and-folders"></a>文件和文件夹访问控制列表

访问控制列表 (ACL) 有两种类型：**访问 ACL** 和 **默认 ACL**。

* **访问 ACL**：控制对象访问权限。 文件和文件夹都具有访问 ACL。

* **默认 ACL**：与文件夹关联的 ACL 的“模板”，用于确定在该文件夹下创建的任何子项的访问 ACL。 文件没有默认 ACL。


访问 ACL 和默认 ACL 具有相同的结构。

> [!NOTE]
> 更改父级的默认 ACL 不影响现有子项的访问 ACL 或默认 ACL。
>
>

## <a name="permissions"></a>权限

文件系统对象权限为“读取”、“写入”和“执行”，可对下表中所示的文件和文件夹使用这些权限：

|            |    文件     |   Folder |
|------------|-------------|----------|
| **读取 (R)** | 可以读取文件内容 | 需有“读取”和“执行”权限才能列出文件夹内容 |
| **写入 (W)** | 可以在文件中写入或追加内容 | 需有“写入”和“执行”权限才能在文件夹中创建子项 |
| **执行 (X)** | 不表示 Data Lake Storage Gen1 上下文中的任何内容 | 需要遍历文件夹的子项 |

### <a name="short-forms-for-permissions"></a>权限的简短形式

**RWX** 用于表示“读取 + 写入 + 执行”。 还有更精简的数字形式，“读取=4”，“写入=2”，“执行=1”，其总和表示各种不同的权限。   下面是一些示例。

| 数字形式 | 简短形式 |      含义     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 读取 + 写入 + 执行 |
| 5            | `R-X`        | 读取 + 执行         |
| 4            | `R--`        | 读取                   |
| 0            | `---`        | 无权限         |


### <a name="permissions-do-not-inherit"></a>权限不继承

在 Data Lake Storage Gen1 使用的 POSIX 样式模型中，项的权限存储在项本身中。 换而言之，无法从父项继承项的权限。

## <a name="common-scenarios-related-to-permissions"></a>与权限相关的常见方案

以下常见方案可帮助你了解对 Data Lake Storage Gen1 帐户执行特定操作所需的权限。

| 操作 | 对象              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| 读取      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| 追加到 | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `-W-`          |
| 删除    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| 创建    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| 列出      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| 列出      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| 列出      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> 只要以上两个条件成立，删除文件时就不需要文件的写入权限。
>
>


## <a name="users-and-identities"></a>用户和标识

每个文件和文件夹都有这些标识的不同权限：

* 拥有用户
* 拥有组
* 命名用户
* 命名组
* 所有其他用户

用户和组的标识是 Azure Active Directory (Azure AD) 标识。 因此除非另外指定“用户”，否则在 Data Lake Storage Gen1 的上下文中可能表示 Azure AD 用户或 Azure AD 安全组。

### <a name="the-super-user"></a>超级用户

超级用户拥有 Data Lake Storage Gen1 帐户中所有用户的大多数权限。 超级用户：

* 拥有 **所有** 文件和文件夹的 RWX 权限。
* 可以更改任何文件或文件夹的权限。
* 可以更改任何文件或文件夹的拥有用户或拥有组。

属于 Data Lake Storage Gen1 帐户“所有者”角色的所有用户都自动成为超级用户。

### <a name="the-owning-user"></a>拥有用户

创建项的用户自动成为该项的拥有用户。 拥有用户可以：

* 更改所拥有文件的权限。
* 更改所拥有文件的拥有组，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 所有者用户无法更改某个文件或文件夹的所有者用户。 只有超级用户可以更改文件或文件夹的拥有用户。
>
>

### <a name="the-owning-group"></a>拥有组

**背景**

在 POSIX ACL 中，每个用户都与“主组”关联。 例如，用户“alice”可能属于“finance”组。 Alice 还可能属于多个组，但始终有一个组指定为她的主组。 在 POSIX 中，当 Alice 创建文件时，该文件的拥有组设置为她的主组，在本例中为“finance”。 否则，所有者组的行为类似于为其他用户/组分配的权限。

由于Data Lake Storage Gen1 中没有与用户关联的“主组”，因此将拥有组分配如下。

**为新文件或文件夹分配拥有组**

* **案例 1**：根文件夹“/”。 此文件夹是创建 Data Lake Storage Gen1 帐户时创建的。 在这种情况下，拥有组设置为全零 GUID。  此值不允许任何访问。  在分配组之前，它是一个占位符。
* **案例 2**（任何其他案例）：创建新项时，从父文件夹复制拥有组。

**更改拥有组**

拥有组可由以下用户更改：
* 任何超级用户。
* 拥有用户，前提是该拥有用户也是目标组的成员。

> [!NOTE]
> 所有者组无法更改某个文件或文件夹的 ACL。
>
> 对于在 2018 年 9 月或之前创建的帐户，拥有组设置为在上面 **案例 1** 的根文件夹情况下创建帐户的用户。  单个用户帐户无法通过拥有组提供权限，因此此默认设置不授予任何权限。 可以将此权限分配给有效的用户组。


## <a name="access-check-algorithm"></a>访问检查算法

以下伪代码显示了 Data Lake Storage Gen1 帐户的访问检查算法。

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>掩码

如访问检查算法中所示，掩码会限制对“命名用户”、“负责人组”和“命名组”的访问权限。  

> [!NOTE]
> 对于新 Data Lake Storage Gen1 帐户，根文件夹（“/”）的访问 ACL 的掩码默认为 RWX。
>
>

### <a name="the-sticky-bit"></a>粘滞位

粘滞位是 POSIX 文件系统的更高级功能。 在 Data Lake Storage Gen1 的上下文中，不太可能需要粘滞位。 总之，如果文件夹上已启用粘滞位，则子项只能由子项的负责人用户删除或重命名。

粘滞位不会显示在 Azure 门户中。

## <a name="default-permissions-on-new-files-and-folders"></a>新文件和文件夹的默认权限

在现有文件夹下面创建新文件或文件夹时，父文件夹的默认 ACL 会确定：

- 子文件夹的默认 ACL 和访问 ACL。
- 子文件的访问 ACL（文件没有默认 ACL）。

### <a name="umask"></a>umask

创建文件或文件夹时，umask 用于修改默认 ACL 在子项上的设置方式。 umask 是父文件夹上的 9 位值，其中包含“负责人用户”、“负责人组”和“其他”的 RWX 值  。

Azure Data Lake Storage Gen1 的 umask 是设置为 007 的常量值。 此值将转换为

| umask 组件     | 数字形式 | 简短形式 | 含义 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 对于负责人用户，将父项的默认 ACL 复制到子项的访问 ACL | 
| umask.owning_group  |    0         |   `---`      | 对于负责人组，将父项的默认 ACL 复制到子项的访问 ACL | 
| umask.other         |    7         |   `RWX`      | 对于其他，删除子项的访问 ACL 的所有权限 |

Azure Data Lake Storage Gen1 的 umask 值实际上表示无论默认 ACL 作何指示，默认情况下永远不会在新子项上传输“其他”的值。 

以下伪代码显示了在为子项创建 ACL 时如何应用 umask。

```
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>有关 Data Lake Storage Gen1 中 ACL 的常见问题

### <a name="do-i-have-to-enable-support-for-acls"></a>是否必须启用 ACL 的支持？

否。 Data Lake Storage Gen1 帐户始终启用了通过 ACL 进行的访问控制。

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>以递归方式删除文件夹及其内容需要哪些权限？

* 父文件夹必须拥有“写入 + 执行”权限。
* 要删除的文件夹及其中的每个文件夹都需要“读取 + 写入 + 执行”权限。

> [!NOTE]
> 不需要有“写入”权限即可删除文件夹中的文件。 但是，**永远无法** 删除根文件夹“/”。
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>谁是为文件或文件夹的所有者？

文件或文件夹的创建者将成为所有者。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>创建文件或文件夹时哪个组将设置为其拥有组？

拥有组是从新文件或文件夹创建所在的父文件夹的拥有组复制而来的。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>文件的拥有用户没有所需的 RWX 权限， 我该怎么办？

拥有用户只需更改文件的权限，即可自动获得所需的任何 RWX 权限。

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>在 Azure 门户中查看 ACL 时看到的是用户名，但通过 API 查看时，看到的却是 GUID，这是为什么？

ACL 中的项存储为 GUID，它们对应于 Azure AD 中的用户。 API 将按原样返回 GUID。 Azure 门户会尽可能地将 GUID 转换为友好名称，以方便使用 ACL。

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>使用 Azure 门户时，为什么有时会在 ACL 中看到 GUID？

如果用户在 Azure AD 中不再存在，会显示 GUID。 当用户离职，或者其帐户已在 Azure AD 中删除时，往往会发生这种情况。 此外，请确保使用正确的 ID 来设置 ACL（将在下面的问题中详细讨论）。

### <a name="when-using-service-principal-what-id-should-i-use-to-set-acls"></a>使用服务主体时，我应该使用什么 ID 来设置 ACL？

在 Azure 门户中，转到“Azure Active Directory”->“企业应用程序”，然后选择你的应用程序。 “概述”选项卡应显示对象 ID，这是添加用于数据访问的 ACL（而不是应用程序 ID）时应使用的 ID。

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Data Lake Storage Gen1 是否支持 ACL 继承？

不支持。但是，可以使用默认 ACL 来设置父文件夹下新建的子文件和文件夹的 ACL。

### <a name="what-are-the-limits-for-acl-entries-on-files-and-folders"></a>文件和文件夹的 ACL 条目有哪些限制？

每个文件和每个目录都可以设置 32 个 ACL。 访问 ACL 和默认 ACL 都有自己的 32 个 ACL 条目的限制。 如果可能，请使用安全组进行 ACL 分配。 通过使用组，你不太可能超出每个文件或目录的最大 ACL 条目数。

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>在哪里可以了解 POSIX 访问控制模型的详细信息？

* [Linux 上的 POSIX 访问控制列表](https://www.linux.com/news/posix-acls-linux)
* [HDFS 权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu 上的 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [在 Linux 上使用访问控制列表 (ACL)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>另请参阅

* [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)
