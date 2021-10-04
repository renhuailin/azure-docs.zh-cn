---
title: 使用 JavaScript (Node.js) 在 Azure Data Lake Storage Gen2 中管理 ACL
description: 使用适用于 JavaScript 的 Azure Storage Data Lake 客户端库在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: cf09d9956a2cc7cea6831b52df45abc7f0fbb1b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555444"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 Node.js 中的 JavaScript SDK 设置 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 Node.js 来获取、设置和更新目录与文件的访问控制列表。

“[包(节点包管理器)](https://www.npmjs.com/package/@azure/storage-file-datalake)” | ”[示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)” | ”[提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)”

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.6.0` 或更高版本。

- 以下安全权限之一：

  - 一个预配的 Azure Active Directory (AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 计划将 ACL 设置应用到的目标容器或目录的拥有用户。 为了以递归方式设置 ACL，这包括目标容器或目录中的所有子项。

  - 存储帐户密钥。

## <a name="set-up-your-project"></a>设置项目

打开一个终端窗口，然后键入以下命令来安装适用于 JavaScript 的 Data Lake 客户端库。

```javascript
npm install @azure/storage-file-datalake
```

将此语句放置在代码文件的顶部，以导入 `storage-file-datalake` 包。

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](./data-lake-storage-access-control-model.md)。

可以使用[适用于 JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)，通过 Azure AD 对应用程序进行身份验证。

获取客户端 ID、客户端机密和租户 ID。 若要执行此操作，请参阅[从 Azure AD 获取用于对客户端应用程序的请求进行授权的令牌](../common/storage-auth-aad-app.md)。 在该过程中，你必须为安全主体分配以下 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 角色之一。

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;
}
```

> [!NOTE]
> 有关更多示例，请参阅[适用于 JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)文档。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential =
     new StorageSharedKeyCredential(accountName, accountKey);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;
}

```

> [!NOTE]
> 此授权方法仅适用于 Node.js 应用程序。 如果打算在浏览器中运行代码，则可以使用 Azure Active Directory (AD) 进行授权。

## <a name="get-and-set-a-directory-acl"></a>获取并设置目录 ACL

此示例获取并设置名为 `my-directory` 的目录的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请将空字符串 (`/`) 传递到“DataLakeFileSystemClient.getDirectoryClient”方法。

### <a name="get-and-set-a-file-acl"></a>获取并设置文件 ACL

此示例获取并设置名为 `upload-file.txt` 的文件的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);
}
```

## <a name="see-also"></a>另请参阅

- [包(节点包管理器)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)
- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)
