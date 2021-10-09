---
title: Azure Data Lake Storage Gen2 的访问控制模型
description: 了解如何在具有分层命名空间的帐户中配置容器、目录和文件级别的访问权限。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 5861d97f1adffc6d92bb702b4ce59a34c103d9f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600259"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的访问控制模型

Data Lake Storage Gen2 支持以下授权机制：

- 共享密钥授权
- 共享访问签名 (SAS) 授权
- 基于角色的访问控制 (Azure RBAC)
- 访问控制列表 (ACL)

[共享密钥和 SAS 授权](#shared-key-and-shared-access-signature-sas-authorization)向用户（或应用程序）授予访问权限，不要求其在 Azure Active Directory (Azure AD) 中有标识。 使用这两种形式的身份验证时，Azure RBAC 和 ACL 不起作用。

Azure RBAC 和 ACL 都要求用户（或应用程序）在 Azure AD 中有标识。 使用 Azure RBAC，你可以授予对存储帐户数据的“粗粒度”访问权限（例如，对存储帐户中 **所有** 数据的读取或写入访问权限），而使用 ACL，你可以授予“细粒度”访问权限，例如，对特定目录或文件的写入访问权限。

本文重点介绍 Azure RBAC 和 ACL，以及系统如何将它们一起评估，以便针对存储帐户资源做出授权决策。

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>基于角色的访问控制 (Azure RBAC)

Azure RBAC 使用角色分配向[安全主体](../../role-based-access-control/overview.md#security-principal)应用权限集。 安全主体是一个对象，表示在 Azure Active Directory (AD) 中定义的用户、组、服务主体或托管标识。 权限集可以向安全主体授予“粗粒度”级别的访问权限，例如，对存储帐户中 **所有** 数据或容器中 **所有** 数据的读取或写入访问权限。

以下角色允许安全主体访问存储帐户中的数据。

|角色|描述|
|--|--|
| [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | 对 Blob 存储容器和数据的完全访问权限。 此访问权限允许安全主体设置项的所有者，以及修改所有项的 ACL。 |
| [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | 对 Blob 存储容器和 Blob 的读取、写入和删除访问权限。 此访问权限不允许安全主体设置项的所有权，但它可以修改安全主体拥有的项的 ACL。 |
| [存储 Blob 数据读者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | 读取和列出存储容器与 Blob。 |

[所有者](../../role-based-access-control/built-in-roles.md#owner)、[参与者](../../role-based-access-control/built-in-roles.md#contributor)、[读取者](../../role-based-access-control/built-in-roles.md#reader)和[存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)等角色允许安全主体管理存储帐户，但不提供对该帐户中数据的访问权限。 但是，这些角色（**读取者** 除外）可以获取对存储密钥的访问权限，存储密钥可在各种客户端工具中用来访问数据。

## <a name="access-control-lists-acls"></a>访问控制列表 (ACL)

使用 ACL，你可以对目录和文件应用“更细粒度”的访问级别。 ACL 是一个包含一系列 ACL 条目的权限构造。 每个 ACL 条目都将安全主体与一个访问级别相关联。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)。

## <a name="how-permissions-are-evaluated"></a>权限是如何评估的

在基于安全主体的授权过程中，将按以下顺序对权限进行评估。

:one:&nbsp;&nbsp;Azure 角色分配将首先进行评估，优先于任何 ACL 分配。

:two:&nbsp;&nbsp;如果根据 Azure 角色分配向操作完全授权，则根本不会评估 ACL。

:three:&nbsp;&nbsp;如果操作未完全获得授权，则会评估 ACL。

> [!div class="mx-imgBorder"]
> ![Data Lake Storage 权限流](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

由于系统评估访问权限的方式，你 **无法** 使用 ACL 来 **限制** 已通过角色分配授予的访问权限。 这是因为系统会首先评估 Azure 角色分配，如果该分配授予了足够的访问权限，则会忽略 ACL。

下图显示了这三个常见操作的权限流：列出目录内容、读取文件和写入文件。

> [!div class="mx-imgBorder"]
> ![Data Lake Storage 权限流示例](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>权限表：组合使用 Azure RBAC 和 ACL

下表显示了如何组合使用 Azure 角色和 ACL 条目，以便安全主体可以执行“操作”列中列出的操作。 此表显示的列代表虚构目录层次结构的每个级别。 容器的根目录 (`/`)、名为 **Oregon** 的子目录、Oregon 目录的名为 **Portland** 的子目录以及 Portland 目录中名为 **Data.txt** 的文本文件分别对应一个列。 这些列中显示了授予权限所需的 ACL 条目的[简短形式](data-lake-storage-access-control.md#short-forms-for-permissions)的表示形式。 如果某个 ACL 条目不是执行操作所必需的，则列中将显示 **N/A**（不适用）。

|    操作             | 分配的 Azure 角色               |    /        | Oregon/     | Portland/ | Data.txt |
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | 不可用      | 不可用      | 不可用       | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   None                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `-WX`     | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 不适用    |
| Create Data.txt          |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `-WX`     | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 不适用    |
| List /                   |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | 不可用      | 不可用      | 不可用       | 不可用    |
|                          |   None                           | `R-X`    | 不适用      | 不可用       | 不适用    |
| List /Oregon/            |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | 不可用      | 不可用      | 不可用       | 不可用    |
|                          |   None                           | `--X`    | `R-X`    | 不适用       | 不适用    |
| List /Oregon/Portland/   |   存储 Blob 数据所有者        | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据参与者  | 不适用      | 不可用      | 不可用       | 不适用    |
|                          |   存储 Blob 数据读取者       | 不可用      | 不可用      | 不可用       | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `R-X`     | 不适用    |

> [!NOTE]
> 若要在 Azure 存储资源管理器中查看容器的内容，安全主体必须[使用 Azure AD 登录到存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)，并且（至少）对容器的根文件夹 (`\`) 具有读取访问权限 (R--)。 此权限级别允许安全主体列出根文件夹的内容。 如果不希望根文件夹的内容可见，可以向安全主体分配[读取者](../../role-based-access-control/built-in-roles.md#reader)角色。 使用该角色，安全主体将能够列出帐户中的容器，但不能列出容器内容。 然后，你可以使用 ACL 授予对特定目录和文件的访问权限。

## <a name="security-groups"></a>安全组

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>对 Azure 角色分配和 ACL 条目的限制

通过使用组，你不太可能超出每个订阅的角色分配的最大数量，以及每个文件或目录的 ACL 条目的最大数量。 下表介绍了这些限制。

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)]

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>共享密钥和共享访问签名 (SAS) 授权

Azure Data Lake Storage Gen2 还支持使用[共享密钥](/rest/api/storageservices/authorize-with-shared-key)和 [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 方法进行身份验证。 这两种身份验证方法的特点是没有与调用方关联的标识，因此不能执行基于安全主体权限的身份验证。

就共享密钥这一种方法而言，调用方有效地获得了“超级用户”访问权限，这意味着对所有资源上所有操作（包括数据操作、设置所有者的操作和更改 ACL 的操作）的完全访问权限。

SAS 令牌本身就包含允许的权限。 它包含的权限有效地应用到所有授权决策，但不执行额外的 ACL 检查。

## <a name="next-steps"></a>后续步骤

若要详细了解访问控制列表，请参阅 [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)。
