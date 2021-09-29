---
title: 使用 AzCopy 和 Azure Active Directory 授予对 blob 的访问权限 | Microsoft Docs
description: 可以通过使用 Azure Active Directory (Azure AD) 为 AzCopy 操作提供授权凭据。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/01/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d5aced875863f892ec9dda0022f4571ce15349c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638228"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>使用 AzCopy 和 Azure Active Directory (Azure AD) 授予对 blob 的访问权限

你可以通过使用 Azure AD 来向 AzCopy 提供授权凭据。 这样就无需为每个命令都追加共享访问签名 (SAS) 令牌。

首先请验证你的角色分配。 然后，选择要授权的 *安全主体* 类型。 [用户标识](../../active-directory/fundamentals/add-users-azure-active-directory.md)、[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)和[服务主体](../../active-directory/develop/app-objects-and-service-principals.md)，每一种都是安全主体类型。

用户标识是在 Azure AD 中具有标识的任何用户。 这是最易于授权的安全主体。 如果你计划在以无用户交互方式运行的脚本中使用 AzCopy，那么托管标识和服务主体会是非常有用的选项。 托管标识更适合从 Azure 虚拟机 (VM) 运行的脚本，服务主体则更适合在本地运行的脚本。

有关 AzCopy 的详细信息，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。

## <a name="verify-role-assignments"></a>验证角色分配

所需的授权级别取决于是要上传文件，还是只下载文件。

如果你只是想下载文件，请验证是否已将[存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色分配到你的用户标识、托管标识或服务主体。

若要上传文件，请验证是否已将以下角色之一分配到了你的安全主体：

- [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

可在以下任何范围内将这些角色分配到安全主体：

- 容器（文件系统）
- 存储帐户
- 资源组
- 订阅

若要了解如何验证和分配角色，请参阅[分配用于访问 blob 数据的 Azure 角色](../blobs/assign-azure-role-data-access.md)。

> [!NOTE]
> 请记住，Azure 角色分配可能需要最多五分钟的时间进行传播。

如果安全主体已添加到目标容器或目录的访问控制列表 (ACL)，则无需将这些角色之一分配到安全主体。 在 ACL 中，安全主体需要对目标目录拥有写入权限，并对容器和每个父目录拥有执行权限。

有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](../blobs/data-lake-storage-access-control-model.md)。

## <a name="authorize-a-user-identity"></a>授权用户标识

验证你的用户标识已获得所需的授权级别后，打开命令提示符，然后键入以下命令并按 ENTER 键。

```azcopy
azcopy login
```

如果收到错误，请尝试包括存储帐户所属组织的租户 ID。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，可以关闭浏览器窗口，开始使用 AzCopy。

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>授权托管标识

如果你打算在无需用户交互即可运行的脚本以及从 Azure 虚拟机 (VM) 运行的脚本中使用 AzCopy，此选项非常有用。 使用此选项时，无需在 VM 上存储任何凭据。

通过使用已在 VM 上启用的系统范围的托管标识，或者通过使用已分配到 VM 的用户分配托管标识的客户端 ID、对象 ID 或资源 ID，可以登录到帐户。

若要详细了解如何启用系统范围的托管标识或创建用户分配的托管标识，请参阅[使用 Azure 门户为 VM 上的 Azure 资源配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)。

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>通过使用系统范围的托管标识来授权

首先，确保已在 VM 上启用系统范围的托管标识。 请参阅[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。

然后，在命令控制台中，键入以下命令并按 ENTER 键。

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>通过使用用户分配的托管标识来授权

首先，确保已在 VM 上启用用户分配的托管标识。 请参阅[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。

然后，在命令控制台中，键入以下任意命令并按 ENTER 键。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

请将 `<client-id>` 占位符替换为用户分配的托管标识的客户端 ID。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

请将 `<object-id>` 占位符替换为用户分配的托管标识的对象 ID。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

请将 `<resource-id>` 占位符替换为用户分配的托管标识的资源 ID。

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>授权服务主体

如果你打算在无需用户交互即可运行（尤其是在本地运行）的脚本中使用 AzCopy，此选项非常有用。 如果你打算在 Azure 中运行的 VM 上运行 AzCopy，则最好是使用托管服务标识，因为它更易于管理。 有关详细信息，请参阅本文的[授权托管标识](#authorize-a-managed-identity)部分。

在运行脚本之前，必须至少以交互方式登录一次，以便能够向 AzCopy 提供服务主体凭据。  这些凭据存储在受保护的已加密文件中，因此，脚本无需提供敏感信息。

可以使用客户端机密或使用与服务主体应用注册关联的证书的密码登录到帐户。

若要详细了解如何创建服务主体，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

若要了解有关服务主体的一般性详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](../../active-directory/develop/app-objects-and-service-principals.md)。

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>通过使用客户端密码来授权服务主体

首先将 `AZCOPY_SPA_CLIENT_SECRET` 环境变量设置为服务主体应用注册的客户端机密。

> [!NOTE]
> 确保在命令提示符下设置此值，而不要在操作系统的环境变量设置中进行设置。 这样，该值就只能在当前会话中使用。

本示例演示如何在 PowerShell 中执行此操作。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 考虑使用本示例中所示的提示符。 这样，密码就不会显示在控制台的命令历史记录中。

接下来键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

请将 `<application-id>` 占位符替换为服务主体应用注册的应用程序 ID。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>通过使用证书来授权服务主体

如果希望使用自己的凭据进行授权，可将证书上传到应用注册，然后使用该证书来登录。

除了将证书上传到应用注册以外，还需要在运行 AzCopy 的计算机或 VM 中保存该证书的副本。 此证书副本应采用 .PFX 或 .PEM 格式，且必须包含私钥。 该私钥应通过密码予以保护。 如果使用的是 Windows，且证书仅在证书存储中，请确保将该证书导出到 PFX 文件（包括私钥）。 有关指导，请参阅 [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate)

接下来，将 `AZCOPY_SPA_CERT_PASSWORD` 环境变量设为证书密码。

> [!NOTE]
> 确保在命令提示符下设置此值，而不要在操作系统的环境变量设置中进行设置。 这样，该值就只能在当前会话中使用。

本示例演示如何在 PowerShell 中执行此任务。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下来键入以下命令，然后按 ENTER 键。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

请将 `<path-to-certificate-file>` 占位符替换为证书文件的相对路径或完全限定的路径。 AzCopy 将保存此证书的路径，但并不会保存此证书的副本，因此，请务必将此证书放在原有位置。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

> [!NOTE]
> 考虑使用本示例中所示的提示符。 这样，密码就不会显示在控制台的命令历史记录中。

## <a name="authorize-without-a-secret-store"></a>不使用机密存储授权

`azcopy login` 命令检索 OAuth 令牌，然后将该令牌放入系统上的机密存储。 如果操作系统没有机密存储（如 Linux keyring），则 `azcopy login` 命令将不起作用，因为没有地方可以放置令牌。

可以设置内存中环境变量来代替使用 `azcopy login` 命令。 然后运行任何 AzCopy 命令。 AzCopy 将检索完成操作所需的 Auth 令牌。 操作完成后，令牌将从内存中消失。

### <a name="authorize-a-user-identity"></a>授权用户标识

验证你的用户标识已获得所需的授权级别后，键入以下命令并按 Enter。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

然后，运行任何 azcopy 命令（例如 `azcopy list https://contoso.blob.core.windows.net`）。

此命令返回身份验证代码和网站的 URL。 打开网站，提供代码，然后选择“下一步”按钮。

![创建容器](media/storage-use-azcopy-v10/azcopy-login.png)

此时会出现登录窗口。 在该窗口中，使用 Azure 帐户凭据登录到 Azure 帐户。 成功登录后，操作即可完成。

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>通过使用系统范围的托管标识来授权

首先，确保已在 VM 上启用系统范围的托管标识。 请参阅[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。

键入以下命令，然后按 Enter。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

然后，运行任何 azcopy 命令（例如 `azcopy list https://contoso.blob.core.windows.net`）。

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>通过使用用户分配的托管标识来授权

首先，确保已在 VM 上启用用户分配的托管标识。 请参阅[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。

键入以下命令，然后按 Enter。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

然后，键入以下任何命令并按 Enter。

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

请将 `<client-id>` 占位符替换为用户分配的托管标识的客户端 ID。

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

请将 `<object-id>` 占位符替换为用户分配的托管标识的对象 ID。

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

请将 `<resource-id>` 占位符替换为用户分配的托管标识的资源 ID。

设置这些变量后，可以运行任何 azcopy 命令（例如 `azcopy list https://contoso.blob.core.windows.net`）。

### <a name="authorize-a-service-principal"></a>授权服务主体

可以使用客户端机密或使用与服务主体应用注册关联的证书的密码登录到帐户。

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>通过使用客户端密码来授权服务主体

键入以下命令，然后按 Enter。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
export AZCOPY_TENANT_ID=<tenant-id>
```

请将 `<application-id>` 占位符替换为服务主体应用注册的应用程序 ID。 将 `<client-secret>` 占位符替换为客户端机密。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

> [!NOTE]
> 考虑使用提示从用户处收集密码。 这样，密码就不会显示在命令历史记录中。

然后，运行任何 azcopy 命令（例如 `azcopy list https://contoso.blob.core.windows.net`）。

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>通过使用证书来授权服务主体

如果希望使用自己的凭据进行授权，可将证书上传到应用注册，然后使用该证书来登录。

除了将证书上传到应用注册以外，还需要在运行 AzCopy 的计算机或 VM 中保存该证书的副本。 此证书副本应采用 .PFX 或 .PEM 格式，且必须包含私钥。 该私钥应通过密码予以保护。

键入以下命令，然后按 Enter。

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
export AZCOPY_TENANT_ID=<tenant-id>
```

请将 `<path-to-certificate-file>` 占位符替换为证书文件的相对路径或完全限定的路径。 AzCopy 将保存此证书的路径，但并不会保存此证书的副本，因此，请务必将此证书放在原有位置。 将 `<certificate-password>` 占位符替换为证书的密码。 请将 `<tenant-id>` 占位符替换为存储帐户所属组织的租户 ID。 若要查找租户 ID，请在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

> [!NOTE]
> 考虑使用提示从用户处收集密码。 这样，密码就不会显示在命令历史记录中。

然后，运行任何 azcopy 命令（例如 `azcopy list https://contoso.blob.core.windows.net`）。

## <a name="next-steps"></a>后续步骤

- 有关 AzCopy 的详细信息，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)

- 如有任何疑问、问题或一般反馈，请在 [GitHub](https://github.com/Azure/azure-storage-azcopy) 页上提交。
