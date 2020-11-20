---
title: Azure Data Lake Storage Gen2 的访问控制模型 |Microsoft Docs
description: 了解如何在具有分层命名空间的帐户中配置容器、目录和文件级别的访问权限。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: normesta
ms.openlocfilehash: 3ddcbe57112251a428e11d6c164cdb1224553f98
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959197"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中的访问控制模型

Data Lake Storage Gen2 支持以下授权机制：

- 共享密钥授权
-  (SAS) 身份验证的共享访问签名
- 基于角色的访问控制 (Azure RBAC) 
- 访问控制列表 (ACL)

[共享密钥和 SAS 授权](#shared-key-and-shared-access-signature-sas-authorization) 向用户 (或应用程序) 授予访问权限，而无需在 Azure Active Directory (Azure AD) 中使用标识。 使用这两种形式的身份验证，Azure RBAC 和 Acl 不起作用。

Azure RBAC 和 ACL 都需要用户 (或应用程序) 在 Azure AD 中具有标识。  使用 Azure RBAC，你可以对存储帐户数据（如对存储帐户中 **所有** 数据的读取或写入访问权限）授予 "粗粒度" 访问权限，而 acl 允许授予对特定目录或文件的写访问权限。  

本文重点介绍 Azure RBAC 和 Acl，以及系统如何将它们一起评估，以便对存储帐户资源做出授权决策。

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>基于角色的访问控制 (Azure RBAC) 

Azure RBAC 使用角色分配向 [安全主体](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal)应用权限集。 安全主体是表示用户、组、服务主体或 (AD) Azure Active Directory 中定义的托管标识的对象。 权限集可以为安全主体指定 "粗粒度" 级别的访问权限，例如，对存储帐户中的 **所有** 数据或容器中的 **所有** 数据进行读写访问。 

以下角色允许安全主体访问存储帐户中的数据。 

|角色|说明|
|--|--|
| [存储 Blob 数据所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | 对 Blob 存储容器和数据的完全访问权限。 此访问权限允许安全主体设置项的所有者，以及修改所有项的 Acl。 |
| [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | 读取、写入和删除对 Blob 存储容器和 blob 的访问权限。 此访问不允许安全主体设置项的所有权，但它可以修改安全主体拥有的项的 ACL。 |
| [存储 Blob 数据读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) | 读取和列出 Blob 存储容器和 blob。 |

[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)、[参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)、[读取](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)者和[存储帐户参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)等角色允许安全主体来管理存储帐户，但不提供对该帐户中数据的访问权限。 但是，这些角色 (排除 **读取器**) 可以获取对存储密钥的访问权限，这些访问权限可用于各种客户端工具来访问数据。

## <a name="access-control-lists-acls"></a>访问控制列表 (ACL)

Acl 使你能够对目录和文件应用 "更精细的访问" 级别。 *Acl* 是包含一系列 *ACL 条目* 的权限构造。 每个 ACL 条目将安全主体与访问级别相关联。  若要了解详细信息，请参阅 [Azure Data Lake Storage Gen2 中 (acl) 的访问控制列表](data-lake-storage-access-control.md)。

## <a name="how-permissions-are-evaluated"></a>如何评估权限

在基于安全主体的授权期间，按以下顺序对权限进行评估。

：一个： &nbsp; &nbsp; 首先评估 Azure 角色分配，并优先于任何 ACL 分配。

：2： &nbsp; &nbsp; 如果操作是根据 Azure 角色分配完全授权的，则根本不会评估 acl。

：三： &nbsp; &nbsp; 如果操作未完全获得授权，则会评估 acl。

> [!div class="mx-imgBorder"]
> ![data lake 存储权限流](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

由于系统评估访问权限的方式，你 **无法** 使用 ACL 来 **限制** 角色分配已授予的访问权限。 这是因为系统会先评估 Azure 角色分配，如果分配授予足够的访问权限，则会忽略 Acl。 

下图显示了三个常见操作的权限流：列出目录内容、读取文件和写入文件。

> [!div class="mx-imgBorder"]
> ![data lake 存储权限流示例](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>权限表：结合 Azure RBAC 和 ACL

下表显示了如何合并 Azure 角色和 ACL 条目，以便安全主体可以执行 " **操作** " 列中列出的操作。 此表显示一个表示虚拟目录层次结构的每个级别的列。 容器的根目录有一列 (`/`) ，一个名为 **俄勒冈** 的子目录，一个名为 " **上海**" 的俄勒冈目录的子目录，以及名为 " **Data.txt**" 的 "上" 目录中的文本文件。 在这些列中显示的是授予权限所需的 ACL 条目的 [简短](data-lake-storage-access-control.md#short-forms-for-permissions) 形式。 如果执行操作不需要 ACL 条目，则列中将 **显示 (** _不适用_) 。

|    Operation             | 分配的 RBAC 角色               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |  
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | 空值      | 空值      | 空值       | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   无                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `-WX`     | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 空值    |
| Create Data.txt          |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | `--X`    | `--X`    | `-WX`     | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 空值    |
| List /                   |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | 空值      | 空值      | 空值       | 不可用    |
|                          |   None                           | `R-X`    | 空值      | 空值       | 空值    |
| List /Oregon/            |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | 空值      | 空值      | 空值       | 不可用    |
|                          |   None                           | `--X`    | `R-X`    | 空值       | 空值    |
| List /Oregon/Portland/   |   存储 Blob 数据所有者        | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据参与者  | 空值      | 空值      | 空值       | 空值    |
|                          |   存储 Blob 数据读取者       | 空值      | 空值      | 空值       | 不可用    |
|                          |   None                           | `--X`    | `--X`    | `R-X`     | 空值    |


> [!NOTE] 
> 若要查看 Azure 存储资源管理器中的容器的内容，安全主体必须 [使用 Azure AD 登录存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#add-a-resource-via-azure-ad)，并 (至少) 具有读取访问权限 (R--)  (容器的根文件夹) `\` 。 此级别的权限允许用户列出根文件夹的内容。 如果不希望根文件夹的内容可见，可以为其分配 " [读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) " 角色。 利用该角色，他们可以列出帐户中的容器，但不能列出容器内容。 然后，你可以使用 Acl 向特定目录和文件授予访问权限。   

## <a name="security-groups"></a>安全组

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Azure 角色分配和 ACL 条目的限制

通过使用组，您不太可能超过每个订阅的角色分配的最大数目，以及每个文件或目录的 ACL 条目的最大数量。 下表介绍了这些限制。

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>共享密钥和共享访问签名 (SAS) 授权

Azure Data Lake Storage Gen2 还支持 [共享密钥](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) 和 [SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview?toc=/azure/storage/blobs/toc.json) 方法进行身份验证。 这两种身份验证方法的特点是没有与调用方关联的标识，因此不能执行基于安全主体权限的身份验证。

对于共享密钥，调用方有效地获得 "超级用户" 访问权限，这意味着对所有资源（包括数据、设置所有者和更改 Acl）的所有操作具有完全访问权限。

SAS 令牌本身就包含允许的权限。 它包含的权限有效地应用到所有授权决策，但不执行额外的 ACL 检查。

## <a name="next-steps"></a>后续步骤

若要了解有关访问控制列表的详细信息，请参阅  [Azure Data Lake Storage Gen2 中 (acl) 的访问控制列表](data-lake-storage-access-control.md)。

