---
title: 教程`:`使用托管标识访问 Azure 存储 - Linux - Azure AD
description: 本教程将指导你完成使用 Linux VM 系统分配的托管标识访问 Azure 存储的过程。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b52bf79589040a585d50700b4369145c7fd98c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737277"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>教程：使用 Linux VM 系统分配的托管标识访问 Azure 存储 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Linux 虚拟机 (VM) 的系统分配托管标识访问 Azure 存储。 学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 在存储帐户中创建 Blob 容器
> * 向 Linux VM 的托管标识授予对 Azure 存储容器的访问权限
> * 获取访问令牌并使用它来调用 Azure 存储

## <a name="prerequisites"></a>必备条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 通过 Azure 门户或每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](/cli/azure/install-azure-cli)（2.0.23 或更高版本）。

## <a name="create-a-storage-account"></a>创建存储帐户 

在本部分中，创建一个存储帐户。 

1. 单击 Azure 门户左上角的“+ 创建资源”按钮。
2. 单击“存储”，然后单击“存储帐户 - Blob、文件、表、队列”。
3. 在“名称”下，输入存储帐户的名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“存储(常规用途 v1)”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。  
6. 单击“创建”。

    ![新建存储帐户](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>创建 blob 容器，并将文件上传到存储帐户

文件需要 blob 存储，因此你需要创建用于存储文件的 blob 容器。 然后将文件上传到新存储帐户中的 Blob 容器。

1. 导航回新创建的存储帐户。
2. 单击“Blob 服务”下的“容器” 。
3. 单击页面顶部的“+ 容器”。
4. 在“新建容器”下，为容器输入一个名称，在“公共访问级别”下，保留默认值。

    ![创建存储容器](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 使用你选择的编辑器，在本地计算机上创建一个标题为 *hello world.txt* 的文件。  打开该文件并添加文本“Hello world! :）”（不包括引号），然后保存该文件。 

6. 通过单击容器名称并单击“上传”，将该文件上传到新创建的容器。
7. 在“上传 blob”窗格中，在“文件”下，单击文件夹图标并浏览到本地计算机上的文件 **hello_world.txt**，选择该文件，然后单击“上传”。

    ![上传文本文件](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>向 VM 授予对 Azure 存储容器的访问权限 

可以使用 VM 的托管标识检索 Azure 存储 blob 中的数据。 Azure 资源的托管标识可以用来对支持 Azure AD 身份验证的资源进行身份验证。  通过向包含存储帐户的资源组范围中的托管标识分配 [storage-blob-data-reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 角色来授予访问权限。
 
有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

>[!NOTE]
> 有关可用于授予存储权限的各种角色的信息，请查看[使用 Azure Active Directory 对 Blob 和队列授予访问权限](../../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights)
## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>获取访问令牌并使用它来调用 Azure 存储

Azure 存储原生支持 Azure AD 身份验证，因此可以直接接受使用托管标识获取的访问令牌。 在某种程度上，这是将 Azure 存储与 Azure AD 集成，不同于在连接字符串中提供凭据。

若要完成以下步骤，需在此前创建的 VM 中工作，并且需要一个可以连接到它的 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](/windows/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](~/articles/virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Linux 虚拟机，然后在“概览”页中单击“连接”。 复制用于连接到 VM 的字符串。
2. 使用所选的 SSH 客户端连接  到 VM。 
3. 在终端窗口中，使用 CURL 向本地托管标识终结点发出请求，以获取 Azure 存储的访问令牌。
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. 现在使用访问令牌访问 Azure 存储，例如，读取先前上传到容器的示例文件的内容。 将 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>` 和 `<FILE NAME>` 的值替换为先前指定的值，将 `<ACCESS TOKEN>` 替换为上一步返回的令牌。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   响应包含文件内容：

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何启用 Linux VM 系统分配的托管标识来访问 Azure 存储。  若要详细了解 Azure 存储，请参阅：

> [!div class="nextstepaction"]
> [Azure 存储](../../storage/common/storage-introduction.md)