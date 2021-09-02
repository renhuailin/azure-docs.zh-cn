---
title: 配置 Azure Active Directory 访问权限 - Azure 区块链服务
description: 如何为 Azure 区块链服务配置 Azure Active Directory 访问权限
ms.date: 05/11/2021
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: ea08a6cb59bdf5f8a18b05b897b8e6161c86f337
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "122653137"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>如何为 Azure 区块链服务配置 Azure Active Directory 访问权限

本文介绍如何使用 Azure Active Directory (Azure AD) 用户、组或应用程序 ID 授予对 Azure 区块链服务节点的访问权限并与之连接。

[!INCLUDE [Retirement note](./includes/retirement.md)]

Azure AD 提供基于云的标识管理，允许你在整个企业中使用单个标识，并访问 Azure 中的应用程序。 Azure 区块链服务与 Azure AD 集成，并提供 ID 联合身份验证、单一登录和多重身份验证等优势。

## <a name="prerequisites"></a>先决条件

* [使用 Azure 门户创建区块链成员](create-member.md)

## <a name="grant-access"></a>授予访问权限

可以同时授予成员级别和节点级别的访问权限。 授予成员级别的访问权限会反过来授予对该成员下所有节点的访问权限。

### <a name="grant-member-level-access"></a>授予成员级别访问权限

授予成员级别的访问权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到“访问控制 (IAM)”>“添加”>“添加角色分配”。
1. 选择“区块链成员节点访问（预览版）”角色，并添加要授予其访问权限的 Azure AD ID 对象。 Azure AD ID 对象可以是：

    | Azure AD 对象 | 示例 |
    |-----------------|---------|
    | Azure AD 用户   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 组  | `sales@contoso.onmicrosoft.com` |
    | 应用程序 ID  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![添加角色分配](./media/configure-aad/add-role-assignment.png)

1. 选择“保存”。

### <a name="grant-node-level-access"></a>授予节点级别访问权限

可以通过导航到“节点安全性”并单击要授予访问权限的节点名称，授予节点级别访问权限。

选择“区块链成员节点访问（预览版）”角色，并添加要授予其访问权限的 Azure AD ID 对象。

有关详细信息，请参阅[配置 Azure 区块链服务事务节点](configure-transaction-nodes.md#azure-active-directory-access-control)。

## <a name="connect-using-azure-blockchain-connector"></a>使用 Azure 区块链连接器进行连接

从 GitHub 下载或克隆 [Azure 区块链连接器](https://github.com/Microsoft/azure-blockchain-connector/)。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

然后按照“自述文件”中的“快速入门”部分，从源代码构建连接器。

### <a name="connect-using-an-azure-ad-user-account"></a>使用 Azure AD 用户帐户进行连接

1. 运行以下命令，使用 Azure AD 用户帐户进行身份验证。 将 \<myAADDirectory\> 替换为 Azure AD 域。 例如，`yourdomain.onmicrosoft.com`。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory>
    ```

1. Azure AD 提示输入凭据。
1. 使用用户名和密码登录。
1. 身份验证成功后，本地代理将连接到区块链节点。 现在可以将 Geth 客户端与本地终结点连接。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>使用应用程序 ID 连接

许多应用程序使用应用程序 ID 而不是 Azure AD 用户帐户对 Azure AD 进行身份验证。

若要使用应用程序 ID 连接到节点，请将 aadauthcode 替换为 aadclient。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 参数 | 说明 |
|-----------|-------------|
| tenant-id | Azure AD 域，例如 `yourdomain.onmicrosoft.com`
| client-id | Azure AD 中已注册应用程序的客户端 ID
| client-secret | Azure AD 中已注册应用程序的客户端密码

有关如何在 Azure AD 中注册应用程序的详细信息，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>连接移动设备或文本浏览器

对于不可能显示 Azure AD 身份验证弹出项的移动设备或基于文本的浏览器，Azure AD 生成一次性密码。 你可以复制此密码，并在另一个环境中继续进行 Azure AD 身份验证。

若要生成此密码，请将 aadauthcode 替换为 aaddevice。 将 \<myAADDirectory\> 替换为 Azure AD 域。 例如，`yourdomain.onmicrosoft.com`。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>后续步骤

有关 Azure 区块链服务中的数据安全性的详细信息，请参阅 [Azure 区块链服务安全性](data-security.md)。